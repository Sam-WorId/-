## Install BlockBench
First, we will use **BlockBench model file (.bbmodel)** to make a model.  
you can download BlockBench [here](https://www.blockbench.net/downloads).  

> [!TIP]\
> Since we follow BlockBench's modeling, It will be helpful to check [how to modeling and animating](https://www.blockbench.net/wiki/guides/bedrock-modeling).
## Create your own model project
![](https://github.com/user-attachments/assets/d183a96a-b644-4be4-af52-3f2fb5e06ead)  
![](https://github.com/user-attachments/assets/711cb24f-7fba-4687-8ee3-a54195835388)  
* * *
First, you should generate your model as **Generic model**.  
You can create your own model by using **Cube**.

> [!TIP]\
> ⚠️ Mesh is not supported.  
> ⚠️ If your client version is **1.21.3 or less**, cube rotation will be limited. (-45, -22.5, 0, 22.5, 45 degrees with only one axis)  
> 💡 If your client version is **1.21.4 or more**, cube with any rotation can be rendered.

## Create model bone
![](https://github.com/user-attachments/assets/53dfce65-edea-412f-b770-33782885800f)  
* * *
Model bone means **some group of cube**.
- All elements will be rendered as one item display packet per one model bone.
- Model bone can be freely rotated.
- BlockBench supports **animating model** per each model bone.

#### Bone tag
* * *
![](https://github.com/user-attachments/assets/2d315f38-cef9-4e3f-b896-3ba5cd093502)  
* * *
There's some built-in bone tag in BetterModel.

- **h_** - This model bone follows base entity's head rotation.
- **hi_** - This model bone and all of children bones follow base entity's head rotation.
- **b_**, **ob_** or **hitbox** - This model bone is used to hit box.
- **p_** - This bone can be mounted with hitbox.

#### External features
- [Template texture](https://github.com/toxicity188/BetterModel/wiki/Template-texture)

## Import model
* Put your model file to BetterModel's plugin folder.
```
plugins/BetterModel/models - General model
plugins/BetterModel/players - Only for animation without textures
```  
* Execute reload command. 
```
/bettermodel reload
```
* Apply generated resource pack in BetterModel's directory.
```
plugins/BetterModel/build(.zip)
```
* Tests your model to summon as entity.
```
/bettermodel spawn <model> [entity type] [scale]
```

> [!TIP]\
> If you're using external resource to merge resource pack, you can check FAQ in [here](https://github.com/toxicity188/BetterModel/wiki/Merge-with-external-resources).

* * *
![5](https://github.com/user-attachments/assets/26e79e42-d2af-42db-b068-983aefafebc7)  
* * *

## Next docs
- [Animating your own model](https://github.com/toxicity188/BetterModel/wiki/Animating-your-own-model)
