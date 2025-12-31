## Architecture
![0](https://github.com/user-attachments/assets/9ae218ad-8c50-4112-89a1-5c1f3a939713)  
* * *
To use BetterModel's API, you have to know about BM's architecture.

1. **Raw data** - raw data of model file (like .bbmodel)
2. **Blueprint** - converted data to Minecraft's model file
3. **Renderer** - A renderer of blueprint (model, limb) -> Creates render pipeline (a tree of model bone)
4. **Tracker** - A tracker of renderer (entity, dummy) -> Schedules render pipeline and sync with world or entity.

## Repository
![Maven Central Version](https://img.shields.io/maven-central/v/io.github.toxicity188/bettermodel)  
I published BM's API at these repositories.
- [Maven Central](https://central.sonatype.com/artifact/io.github.toxicity188/bettermodel)
- [GitHub Packages](https://github.com/toxicity188/BetterModel/packages/2612087)

#### Release
```kotlin
repositories {
    mavenCentral()
}

dependencies {
    compileOnly("io.github.toxicity188:bettermodel:VERSION")
}
```
#### Snapshot
```kotlin
repositories {
    maven("https://maven.pkg.github.com/toxicity188/BetterModel") {
        credentials {
            username = YOUR_GITHUB_USERNAME
            password = YOUR_GITHUB_TOKEN
        }
    }
}

dependencies {
    compileOnly("io.github.toxicity188:bettermodel:VERSION-SNAPSHOT")
}
```

## API Tester plugin
You can always see my test plugin at [here](https://github.com/toxicity188/BetterModel/blob/master/test-plugin/src/main/java/kr/toxicity/model/test/RollTester.java).  
You can also follow my [MythicMobs support](https://github.com/toxicity188/BetterModel/tree/master/core/src/main/kotlin/kr/toxicity/model/compatibility/mythicmobs/mechanic) to refer how BM's API is work.

## General API usage

#### Gets some model or limb
```java
BetterModel.model("demon_knight"); //A model file in BetterModel/models (for general model with saving)
BetterModel.limb("steve"); //A model file in BetterModel/players (for player model with no saveing)

BetterModel.modelOrNull("demon_knight"); //general model or null
BetterModel.limbOrNull("steve"); //player model or null
```

#### Creates model
You can apply some model to specific entity or player.
```java
EntityTracker tracker = BetterModel.model("demon_knight")
    .map(r -> r.getOrCreate(entity)) //Gets or creates entity tracker by this renderer to some entity.
    .orElse(null);
```
```java
EntityTracker tracker = BetterModel.model("demon_knight")
    .map(r -> r.create(entity, TrackerModifier.DEFAULT, t -> t.update(TrackerUpdateAction.tint(0x0026FF)))) //Creates entity tracker with pre-spawn task.
    .orElse(null);
```
Also, you can create some model to dummy.

```java
DummyTracker tracker = BetterModel.model("demon_knight")
    .map(r -> r.create(location)) //Creates some dummy tracker to this location.
    .orElse(null);

DummyTracker skinTracker = BetterModel.limb("steve")
    .map(r -> r.create(location, ModelProfile.of(player))) //Creates some dummy tracker to this location and player's skin profile.
    .orElse(null);
```

#### Gets entity tracker's registry
```java
BetterModel.registry(uuid); //Gets optional registry from some uuid
BetterModel.registry(entity); //Gets optional registry from some entity if entity has model data.
``` 

#### Checks some entity is hitbox entity
```java
if (entity instanceof HitBox hitbox) {
    Entity source = hitbox.source(); //Gets source entity
    EntityTrackerRegistry source = hitbox.registry().orElse(null); //Gets source entity's registry
}
```

#### Runs animation
```java
BetterModel.registry(entity) //Gets tracker registry.
    .map(reg -> reg.tracker("model")) //Gets tracker by it's name
    .ifPresent(tracker -> tracker.animate("animation_name", AnimationModifier.DEFAULT_WITH_PLAY_ONCE, () -> { 
        //Do stuff when this animation is being removed.
    });
});
```

#### Gets bone
```java
RenderedBone bone = BetterModel.registry(entity) //Gets tracker registry.
    .map(reg -> reg.tracker("model")) //Gets tracker by it's name
    .map(tracker -> tracker.bone("bone_name")) //Searches bone by it's raw name.
    .orElse(null);
```

#### Gets bone's world position
```java
Vector3f wpos = BetterModel.registry(entity) //Gets tracker registry.
    .map(reg -> reg.tracker("model")) //Gets tracker by it's name
    .map(tracker -> tracker.bone("bone_name")) //Searches bone by it's raw name.
    .map(HitBoxSource::hitBoxPosition) //Gets bone's world relative position.
    .orElse(null);
if (wpos == null) return;
Location finalLocation = entity.getLocation().add(wpos.x, wpos.y, wpos.z) //Final bone's world position = base entity's location + bone's position
```

#### Gets bone's currently played animation
```java
RunningAnimation runningAnimation = BetterModel.registry(entity) //Gets tracker registry.
    .map(reg -> reg.tracker("model")) //Gets tracker by it's name
    .map(tracker -> tracker.bone("bone_name")) //Searches bone by it's raw name.
    .map(RenderedBone::runningAnimation) //Gets running animation.
    .orElse(null);
```

#### Hide/Show some model tracker
```java
BetterModel.registry(entity) //Gets tracker registry.
    .map(reg -> reg.tracker("model")) //Gets tracker by it's name
    .ifPresent(tracker -> {
        boolean hideSuccess = tracker.hide(player); //Hide
        boolean showSuccess = tracker.show(player); //Show
    });
```
#### Gets animation's length
```java
BetterModel.limb("steve") //Gets model first
    .flatMap(r -> r.animation("roll")) //Flat map to optional animation
    .map(BlueprintAnimation::length) //Gets animation's length second
    .orElse(0F) //Null value
```

#### Add view/spawn/hide filter
```java
@EventHandler
public void spawn(CreateTrackerEvent event) {
    event.tracker().getPipeline().viewFilter(player -> !player.isAfk()) //Prevents sending animation packet when player is AFK
}
```

#### Removes some tracker
```java
BetterModel.registry(entity) //Gets tracker registry.
    .map(reg -> reg.tracker("model")) //Gets tracker by it's name
    .ifPresent(Tracker::close);
```

#### Removes all tracker of some entity
```java
BetterModel.registry(entity).ifPresent(EntityTrackerRegistry::close);
```

#### Update some tracker's display data
```java
BetterModel.model("demon_knight")
    .map(r -> r.create(entity, TrackerModifier.DEFAULT, t -> {
        t.update(TrackerUpdateAction.tint(rgb)); //Tint
        t.update(TrackerUpdateAction.enchant(true), bone -> true); //Enchant with predicate
    }))
    .ifPresent(tracker -> tracker.update(TrackerUpdateAction.composite( //Composite
        TrackerUpdateAction.brightness(15, 15)  //Brightness
        TrackerUpdateAction.billboard(Display.Billboard.CENTER) //Billboard
    )));
}
```

#### Animate animation per specific player
```java
//Creates animation modifier
AnimationModifier modifier = AnimationModifier.builder()
    .player(player) //Sets target player
    .type(AnimationIterator.Type.PLAY_ONCE)
    .build()

BetterModel.registry(entity) //Gets tracker registry.
    .map(reg -> reg.tracker("model")) //Gets tracker by it's name
    .ifPresent(tracker -> tracker.animate("animation_name", modifier));
```