---
title: "Industrial Automation"
weight: 6
---

# **Automation: The Future of Work is No Work at All**

![2](https://assets.bonappetit.com/photos/5c05882fed3d591355454864/master/pass/tinned-fish-gift-guide-lede.gif)

{{< hint warning >}}
`Disclaimer:`

I apologize for the inconvenience caused by the drawings in this projectbeing in Portuguese. I know it makes it difficult for others to understand the content. I have done my best to translate everything I could, but I acknowledge that there may still be some untranslated elements. I hope that the overall content is still manageable despite these limitations. Nonetheless, I thought this was a funny project so I also decided to include it... because sardines!

It is, in addition, a very typical dish for portuguese food! 😊

{{< /hint >}}

## 1 Why Talk About Sardines and Automation?

Well, because the problem addressed is about the production of canned sardines. Naturally, the process varies from company to company. In this problem, I will consider that the process begins with washing the sardines, followed by cutting, adding a sauce, canning, and cooking. At the end, the cans will be ready for storage and shipping. I also consider that the washing and cutting processes, which are usually done by employees, will be carried out by machines specifically designed for that purpose.

## 2 Problem Guide

### 2.1. Production Cycle (Slave)

The initial configuration of the cycle is with the cylinders retracted, elevator on floor 0, conveyor belt stopped, and traffic lights turned off.

When the raw material arrives at the factory, the first step is to transport it to the facilities where it will be subsequently processed. This process is simulated in the laboratory by activating the conveyor belt through the "PVerde" button or the "dp" presence sensor. The conveyor belt will transport the sardines to the elevator (left-right direction). 

When the batch of sardines reaches the right sensor of the conveyor belt, it stops. The elevator waits 2 seconds for the loading of the set, then rising to the 1st floor.

On the 1st floor, the operations of cleaning and cutting the sardines will be performed. One second after reaching the 1st floor, cleaning begins, simulated by turning on a sequence of traffic lights (red, yellow, green). In this sequence, the four traffic lights of each color light up simultaneously, and each color stays lit for 2 seconds.

Once the cleaning is finished, there is a 1-second pause, then the cutting is performed. The cutting is simulated by a sequence of cylinders: B+B-(A+C+)(A-C-)B+B-.

Simultaneously with the movement of the cylinders, an intermittent alert sound is heard. The signal lasts 0.2 seconds and is emitted every 1 second.

After the cutting, the batch goes to the next station, rising to the 2nd floor. On this floor, the operations of adding sauce to the sardines and canning will be performed. There are three types of sauce available: natural, vegetable oil, and tomato sauce. The choice of type is simulated by buttons 0, 1, or 2, as follows: "botao0" corresponds to natural sauce, "botao1" corresponds to vegetable oil, and "botao2" to tomato sauce.

Any other possibility, such as none of these switches activated, or more than one switch activated, causes the yellow light ("LAmar") to flash intermittently, which turns on for 1 second and turns off for 1 second. After a period of turning on and off the yellow light, the cycle goes back to evaluating the state of the buttons, continuing to flash yellow until it obtains a valid production order.

When a valid option is selected, the addition of sauce is simulated by turning on the traffic lights, turning on the lights of the 1st and 4th traffic lights initially for 2 seconds, and then the lights of the 2nd and 3rd traffic lights for 2 seconds. In the case of natural sauce, the yellow lights turn on, for vegetable oil the green lights turn on, and for tomato sauce the red lights turn on. In each case, the corresponding counter is incremented.

Canning is simulated by a sequence of cylinders: (A+B+C+)(Wait 4 seconds)(A-B-C-).

Once canning is finished, the product goes to the next station, rising to the 3rd floor. On this floor, the cooking of the sardines will be performed. Cooking is done in an oven, simulated by turning on the red traffic lights for 4 seconds.

After cooking, the total product counter is incremented. The product should be unloaded from the elevator. After unloading, the "PVerde" button is pressed and the elevator returns to floor 0. When reaching floor 0, the cycle goes back to waiting for a new production order.


(Grafcet to add later)