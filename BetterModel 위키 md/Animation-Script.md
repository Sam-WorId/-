![](https://github.com/user-attachments/assets/8b9ce538-632a-4b54-84d7-0e046606f9a5)
* * *
We are using **Effects/Instructions** to script animation.

## Script format
![](https://github.com/user-attachments/assets/3a4ec443-93e7-4711-8131-41d521a5c7e2)
* * *
You have to follow this string format to script.
```
name:optional_argument{metadata_key=metadata_value;another_key=another_value}
```
* * *
![](https://github.com/user-attachments/assets/3d31fa78-bd96-4eb9-bd9c-dcac7f60acbe)
* * *
You can define multiple script per each line.

## Built-in script
Like [MEG](https://git.lumine.io/mythiccraft/model-engine-4/-/wikis/Modeling/Scriptable-Keyframes), There's some built-in script.

#### Enchant
argument: none
| metadata key | metadata type |
| :---: | :---: |
| part | (optional) string |
| exact | (optional) boolean |
| children | (optional) boolean |
| enchant | boolean |

#### Remap
argument: none
| metadata key | metadata type |
| :---: | :---: |
| model | string |
| map| string |

#### Partvis
argument: none
| metadata key | metadata type |
| :---: | :---: |
| part | (optional) string |
| exact | (optional) boolean |
| children | (optional) boolean |
| visible | boolean |

#### Tint
argument: none
| metadata key | metadata type |
| :---: | :---: |
| part | (optional) string |
| exact | (optional) boolean |
| children | (optional) boolean |
| color | int |
| damage | boolean |

#### ChangePart
argument: none
| metadata key | metadata type |
| :---: | :---: |
| part | (optional) string |
| exact | (optional) boolean |
| children | (optional) boolean |
| nmodel | string |
| npart | string |

#### Brightness
argument: none
| metadata key | metadata type |
| :---: | :---: |
| part | (optional) string |
| exact | (optional) boolean |
| children | (optional) boolean |
| block | int |
| sky | int |

## MythicMobs skills
![](https://github.com/user-attachments/assets/3cb350a5-9f0d-4b42-93ee-39e6347b5829)
* * *
You can execute MythicMobs skills by this script.
```
mm:skill_name{parameter_name=parameter_value}
```

## Parent docs
- [Animating your own model](https://github.com/toxicity188/BetterModel/wiki/Animating-your-own-model) 
