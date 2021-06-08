# CCI_AVCE_FP_KikiAndWawa

> Brief: Kiki and Wawa are travelers in the world. They explore the world and meet different friends. This is a simple scenario in the game. Players need to control Kiki to find friends: Dogknight, in order to learn new skills. Players can move freely in the world and can also interact with different objects.

![](https://miro.medium.com/max/1920/1*3M7jJdVf0SRY-tNC4ytzng.gif)

### 1.Create roles and camera

After having a rough game concept, we started looking for suitable characters, and finally we chose these free models in the assetstore, which can help us work more efficiently.

![](https://miro.medium.com/max/1080/1*V66qXaUowussE25RiFrYrg.png)

![](https://miro.medium.com/max/1080/1*V4Kwcl5B8N64fKSYj0CEAQ.png)

You can also find them in Unity’s assetstore: [Party Monster](https://assetstore.unity.com/packages/3d/characters/creatures/party-monster-duo-polyart-195698) and [Dog Knight](https://assetstore.unity.com/packages/3d/characters/animals/dog-knight-pbr-polyart-135227)

![](https://miro.medium.com/max/1080/1*saZ8VyAvCs5fEjnj0Cs2Lg.png)

Then, we created a camera script that allows camera move with the character,also players can  adjust the angle with the mouse. `CameraController.cs` is detailed as follows:

```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CameraController : MonoBehaviour
{

    public Transform target;
    public Vector3 offset;
    public bool useOffsetValues;
    public float rotateSpeed;

    public Transform pivot;
    public float maxViewAngle;
    public float minViewAngle;

    public bool invertY;

    // Start is called before the first frame update
    void Start()
    {
        if(!useOffsetValues)
        {
            offset = target.position - transform.position;
        }

        pivot.transform.position = target.transform.position;
        // pivot.transform.parent = target.transform;
        pivot.transform.parent = null;

        Cursor.lockState = CursorLockMode.Locked;
    }

    // Update is called once per frame
    void LateUpdate()
    {


        pivot.transform.position = target.transform.position;
        // Get the x position of the mouse & rotate the target
        float horizontal = Input.GetAxis("Mouse X") * rotateSpeed;
        pivot.Rotate(0, horizontal, 0);

        // Get the Y position of the mouse & rotate the pivot
        float vertical = Input.GetAxis("Mouse Y") * rotateSpeed;
        // pivot.Rotate(-vertical, 0, 0);
        if(invertY)
        {
            pivot.Rotate(vertical, 0, 0);
        } else{
            pivot.Rotate(-vertical, 0, 0);
        }

        // Limit up/down camera rotation
        if(pivot.rotation.eulerAngles.x > maxViewAngle && pivot.rotation.eulerAngles.x < 180f)
        {
            pivot.rotation = Quaternion.Euler(maxViewAngle, 0, 0);
        }

        if(pivot.rotation.eulerAngles.x > 180f && pivot.rotation.eulerAngles.x < 360f + minViewAngle)
        {
            pivot.rotation = Quaternion.Euler(minViewAngle, 0, 0);
        }

        // Move the camera based on the currentv rotation of the target & the original offset
        float desiredYAngle = pivot.eulerAngles.y;
        float desiredXAngle = pivot.eulerAngles.x;

        Quaternion rotation = Quaternion.Euler(desiredXAngle, desiredYAngle, 0);
        transform.position = target.position - (rotation * offset);

        // transform.position = target.position - offset;

        if(transform.position.y < target.position.y)
        {
            transform.position = new Vector3(transform.position.x, target.position.y - .5f, transform.position.z);
        }

        transform.LookAt(target.transform);    
    }
}

```

The effect of testing in Unity is as follows:

![](https://miro.medium.com/max/1080/1*0x0gg_R7ySrDpFlukkjLVA.gif)

Next, we create the character animation, allow the player to control the movement of the character through the keyboard, and create the rigibody and collider so that the character has physical properties. You can view the complete code in `PlayerController.cs`:

```C#
        if(knockBackCounter <= 0)
        {
            float yStore = moveDirection.y;
            moveDirection = (transform.forward * Input.GetAxis("Vertical")) + (transform.right * Input.GetAxis("Horizontal"));
            moveDirection = moveDirection.normalized * moveSpeed;
            moveDirection.y = yStore;

            if(Input.GetButtonDown("Attack"))
            {
                anim.SetInteger("AtkNum", UnityEngine.Random.Range(0, 2));
                anim.SetTrigger("Attack");
            }

            if(Input.GetButtonDown("Dance"))
            {
                anim.SetTrigger("Dance");
            }

            if(Input.GetButtonDown("Defense"))
            {
                anim.SetTrigger("Defense");
            }


            if(controller.isGrounded)
            {
    
                moveDirection.y = 0f;

                if(Input.GetButtonDown("Jump"))
                {
                    moveDirection.y = jumpForce;
                    JumpSound.Play();
                }
            }
        } else {
            knockBackCounter -= Time.deltaTime;

        }
```

The achieved effect is as follows:

![](https://miro.medium.com/max/1080/1*ZDdyrwP1xzBAKeJmqq043g.gif)



### 2.Edit scenes

In this part, we need to create a game space where the characters can move. This part makes me feel very difficult. How to make the space interesting and feel natural is a big challenge.

In the initial idea, we considered choosing the polygon style, because it makes people feel more interesting and relaxed. I purchased assets-[POLYGON Prototype](https://assetstore.unity.com/packages/3d/props/exterior/polygon-prototype-low-poly-3d-art-by-synty-137126) and it is great ! The other two assets are also very helpful: [POLYGON Starter Pack](https://assetstore.unity.com/packages/3d/props/polygon-starter-pack-low-poly-3d-art-by-synty- 156819) and [Free Trees](https://assetstore.unity.com/packages/3d/vegetation/trees/free-trees-103208). 

Editing the map is really a complicated matter, and I spent a lot of time thinking about what elements need to be added.

![](hhttps://miro.medium.com/max/1080/1*8eFbhGUptubL7riUjyZpGA.png)

![](https://miro.medium.com/max/1080/1*7RfCnrr6nytpcrf-vVg-Lw.png)



### 3.Extra elements and sounds

In order to enrich the content of the game, we also introduced some NPCs and gold coins, and added some simple sounds and BGM to make the environment more realistic. Our music is also free material, you can find them here：[*FREE* Retro Game Music](https://assetstore.unity.com/packages/audio/music/free-retro-game-music-47825) and [FREE Music For Puzzle Games](https://assetstore.unity.com/packages/audio/music/free-music-for-puzzle-games-152395). Our NPCs come from：[RPG Monster Duo PBR Polyart](https://assetstore.unity.com/packages/3d/characters/creatures/rpg-monster-duo-pbr-polyart-157762).

![](https://miro.medium.com/max/1080/1*RJ4V3j1vaKvCgVZwbX8_mA.png)

![](https://miro.medium.com/max/1080/1*IMQ1RydwdK8pzCHcbuKE0g.png)



### 4.Add StartScene and QuitScene

After the main game scene is completed, we also need to improve the game's menu page and end page. At this point, our simple game is almost complete, and I want to share some simple game content.

![](https://miro.medium.com/max/1080/1*rwQhy7yy5dvd8Y2vG1l_fA.png)

![](https://miro.medium.com/max/1080/1*nSa4lmxzjKPKV32MFjZw6A.png)

### 5.Preview

Share some game content with you, of course, you can also download the Mac version to try it out! But there are still many bugs in the game, sorry.

![](https://miro.medium.com/max/800/1*SDITmIKe0BbnEmH-O9ASGA.gif)

![](https://miro.medium.com/max/800/1*WIoOhQccuproJb6YiSjB3A.gif)

![](https://miro.medium.com/max/800/1*Al6ZgSdzQ1rLv-daeIMS3g.gif)

![](https://miro.medium.com/max/800/1*5WbQUaSZ-koaanWKuaQVCA.gif)

![](https://miro.medium.com/max/800/1*6G2B4f3ElWaer29YPHCiqg.gif)

![](https://miro.medium.com/max/800/1*mB1e6M7qYHb5bJj1RIDgLQ.gif)

![](https://miro.medium.com/max/800/1*PwX9ldPKeMvPq4cwAVSJSw.gif)



### Reference:

1.Lonely Mountains: Downhill-https://store.steampowered.com/app/711540/Lonely_Mountains_Downhill/

2.For The King-https://store.steampowered.com/app/527230/__For_The_King/

3.Morphite-https://store.steampowered.com/app/661740/Morphite/

4.Hide and Seek-https://store.steampowered.com/app/1010860/Hide_and_Seek/

5.40 Stunning Low Poly Art Images You Need To See-https://sundaysundae.co/stunning-low-poly-art-images-you-need-to-see/

6.A Beginner's Guide to Designing Video Game Levels-https://gamedevelopment.tutsplus.com/tutorials/a-beginners-guide-to-designing-video-game-levels--cms-25662
