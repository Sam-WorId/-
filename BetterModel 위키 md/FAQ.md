### I'm using Nexo, but my model textures show up as PODZOL. How can I fix this?
Set `Pack.import.model_engine.import_pack` to false in your `Nexo/settings.yml`,

then reload your server and resourcepack. This should resolve the issue.

```yaml
    model_engine:
      import_pack: false
      exclude_shaders: false
```

### How can I disable BetterModel's MythicMobs mechanic?
```yaml
module:
  model: false
  player-animation: true
```
You can disable it by set **module.model** to false in config.yml

### Where can I find BetterModel's resource pack?
You can find **plugins/BetterModel/build.zip** by default.