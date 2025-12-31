### Targeting packet

As you know, both ModelEngine and BetterModel use item display packet.  
So we have to profile **minecraft_set_entity_data** packet to assume network usage.

This packet sends a list of data about some entity.
- shared features
- entity's original features

And item display has this entity data.
- item
- transformation(position, rotation, scale)
- glow, interpolation duration/delay, billbard, brightness, etc.

And you can easily estimate that entity data about transformation can be very heavy and consume a lot of network resources.

### Profiling code
It is very difficulty to profile a final buffer size only derived from specific model plugin.  
But we can estimate this by netty channel injection.

```java
@SuppressWarnings("unused")
public final class ModelProfiler extends JavaPlugin {
    private static final DecimalFormat FORMAT = new DecimalFormat("#,###");
    private static final String VANILLA_ENCODER = "encoder";
    private static final Method ENCODE_METHOD;

    static {
        try {
            ENCODE_METHOD = MessageToByteEncoder.class
                    .getDeclaredMethod("encode", ChannelHandlerContext.class, Object.class, ByteBuf.class);
            ENCODE_METHOD.setAccessible(true);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    private final Map<UUID, PlayerProfiler> profilerMap = new ConcurrentHashMap<>();

    @Override
    public void onEnable() {
        Bukkit.getPluginManager().registerEvents(new Listener() {
            @EventHandler
            public void join(PlayerJoinEvent event) {
                var player = event.getPlayer();
                var channel = ((CraftPlayer) player).getHandle().connection.connection.channel;
                channel.eventLoop().submit(() -> profilerMap.computeIfAbsent(player.getUniqueId(), uuid -> new PlayerProfiler(player, channel.pipeline())));
            }
            @EventHandler
            public void quit(PlayerQuitEvent event) {
                var handler = profilerMap.remove(event.getPlayer().getUniqueId());
                if (handler != null) handler.remove();
            }
        }, this);
    }

    private record TimedPacketSize(long time, int bytes) {}

    private class PlayerProfiler extends MessageToByteEncoder<Packet<?>> {
        private final Queue<TimedPacketSize> byteQueue = new ConcurrentLinkedQueue<>();
        private final ScheduledTask actionbar;
        private final MessageToByteEncoder<?> delegate;

        private PlayerProfiler(@NotNull Player player, @NotNull ChannelPipeline pipeline) {
            actionbar = Bukkit.getAsyncScheduler().runAtFixedRate(ModelProfiler.this, task -> {
                var time = System.currentTimeMillis();
                byteQueue.removeIf(t -> time - t.time > 1000);
                if (!byteQueue.isEmpty()) {
                    player.sendActionBar(Component.text("Current traffic usage: " + FORMAT.format(byteQueue.stream()
                            .mapToInt(i -> i.bytes)
                            .sum() / 1024) + " kB/s"));
                }
            }, 50, 50, TimeUnit.MILLISECONDS);
            delegate = (MessageToByteEncoder<?>) pipeline.replace(VANILLA_ENCODER, VANILLA_ENCODER, this);
        }

        private void remove() {
            actionbar.cancel();
        }

        @Override
        protected void encode(ChannelHandlerContext ctx, Packet<?> msg, ByteBuf out) throws Exception {
            var before = out.writerIndex();
            ENCODE_METHOD.invoke(delegate, ctx, msg, out);
            if (msg instanceof ClientboundSetEntityDataPacket) {
                byteQueue.add(new TimedPacketSize(System.currentTimeMillis(), out.writerIndex() - before));
            }
        }
    }
}

```

As you can see, It can filter all of ClientboundSetEntityDataPacket and collect the orignal packet's buffer size per each player.  
It is not totally accurate network resource usage, but It can be a good profiler to check the efficiency of some model plugin.

### Profiling tool
- ModelProfiler in above
- [TrafficProfiler 1.2](https://github.com/toxicity188/TrafficProfiler)
- [Soul Knight created by torotoro](https://youtu.be/5GhtsPM-hrs?si=9XC85wxlKgoZTiW2)

### Profiling step
1. Summon soul knight as husk, testing idle and damage tint
2. Spawn 5 of soul knight and run 90 seconds to get network summary

### Profiling - ModelEngine (4.0.9 - build 2150)
You can see my profiling video at [my Youtube](https://youtu.be/aKhsE26sEp4).  

<img width="720" height="385" alt="meg_1" src="https://github.com/user-attachments/assets/1199e5df-a47b-48ae-8d8e-5db995e139e7" />
<img width="720" height="385" alt="meg_2" src="https://github.com/user-attachments/assets/69e21a36-c073-459b-a13a-36af16e84e2d" />

Generally, ModelEngine's traffic is stable and high.  
It uses approximately >=20kb of data packet even it is idle state.

```json
{
 "total_time": "90002.0ms",
 "average_traffic": {
  "in": "0kbps",
  "out": "449kbps"
 },
 "global": {
  "client_bound": {
   "minecraft_level_chunk_with_light": {
    "value": 26878,
    "percentage": "66.41%"
   },
   "minecraft_set_entity_data": {
    "value": 10802,
    "percentage": "26.69%"
   },
   "minecraft_entity_position_sync": {
    "value": 2009,
    "percentage": "4.965%"
   },
   "..."
  }
 }
}
```
And 90 seconds of summary is 10,802kb of data, and 2,009 of position sync packet. (=12,811kb)

### Profiling - BetterModel (1.9.2 - build 230)
You can see my profiling video at [my Youtube](https://youtu.be/IP-gOV_rFp0).  

<img width="720" height="385" alt="bm_1" src="https://github.com/user-attachments/assets/38f3ea2c-15b9-41c0-986b-af521b80589a" />
<img width="720" height="385" alt="bm_2" src="https://github.com/user-attachments/assets/6a847821-b543-46ea-8c4f-e4dd174682d9" />

Generally, BetterModel's traffic is dynamic(low ~ middle high).  
But It shows low packet usage than ModelEngine in all of animation.  

```json
{
 "total_time": "90002.0ms",
 "average_traffic": {
  "in": "0kbps",
  "out": "449kbps"
 },
 "global": {
  "client_bound": {
   "minecraft_level_chunk_with_light": {
    "value": 27780,
    "percentage": "84.286%"
   },
   "minecraft_set_entity_data": {
    "value": 3769,
    "percentage": "11.436%"
   },
   "minecraft_move_entity_rot": {
    "value": 338,
    "percentage": "1.027%"
   },
   "..."
  }
 }
}
```
And 90 seconds of summary is 3,769kb of data, and 338 of rotation packet. (=4,107kb)  
You can see that BetterModel only consumes **32.05%** size of packet compared with ModelEngine.

### Conclusion
**ModelEngine**  
Packet usage is stable but consistently high.  
For example, idle animations generate almost the same amount of traffic as walk animations.  

Frustum culling appears inefficient.  
It reacts to the player's yaw, but fails to properly account for pitch, leading to unnecessary network traffic even when models are not visible.  

**BetterModel**  
Packet usage is lower and more dynamic.  
Idle animations produce less than half the traffic compared to walk animations.  

Overall, all animations generate significantly less traffic than those in ModelEngine.

It seems that BetterModel is more network-optimized that ModelEngine.  
Maybe It can be a good choice to reduce your network cost.