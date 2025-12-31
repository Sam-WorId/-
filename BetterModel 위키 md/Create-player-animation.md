![](https://github.com/user-attachments/assets/42005349-ff21-4471-8f05-4800468da40f)
* * *
As you can see, BetterModel supports **player model with player's own skin without textures**.

> [!CAUTION]\
> ViaVersion is not supported between <=1.21.3 server/client and >=1.21.4 server/client.  
> Check [ViaVersion support range](https://github.com/toxicity188/BetterModel/wiki/ViaVersion-support-range) to use player limb in your server.  

## Make your animation
* **Step 1**. Go to plugins/BetterModel/**players** folder
* * *
![](https://github.com/user-attachments/assets/07009428-8dbd-44de-a0ca-c1dbbed879c1)
* * *
* **Step 2**. Open **steve.bbmodel** with BlockBench.
* * *
![](https://github.com/user-attachments/assets/16c52791-1d94-47e5-9586-6fcc30f97e0c)  
![](https://github.com/user-attachments/assets/88b21e3e-3b0e-4c1c-8163-e33090bff5f4)
* * *
As you can see, you can find some bone tags for player model.
```
head (ph)
right arm (pra)
right forearm (prfa)
left arm (pla)
left forearm (plfa)
hip (phip)
waist (pw)
chest (pc)
right leg (prl)
right foreleg (prfl)
left leg (pll)
left foreleg (plfl)
left item (pli)
right item (pri)
cape (cape)
```
These bone tags can be used in **general model too**.  
> [!TIP]\
> Model without saving - limb  
> Model with saving - model

* **Step 3**. Animating your own animation
* * *
![7](https://github.com/user-attachments/assets/48b9d94d-586c-4259-9bf7-e7310f35e300)
* * *

## Run your animation
You can easily run your animation to execute this command.
```
/bm play <limb> <animation>
```
* * *
![](https://github.com/user-attachments/assets/f16c9cf3-27e2-4476-88e6-a1ca642dcc63)
* * *

## Shaderspack compatibility
![](https://github.com/user-attachments/assets/034dd64c-6889-4a01-961d-e69679b1c71b)  
* * *
If your server and client version is 1.21.4 or higher, **player model is compatible with Shaderspack.**
