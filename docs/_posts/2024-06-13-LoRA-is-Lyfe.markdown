---
layout: post
title:  "LoRA in the Colonies"
date:   2024-06-13 14:56:31 +1200
categories: LoRA
---

Here I want to try and capture the different gotchas (for my own sanity) on my delightful journey setting up an Arduino-based controller through a LoRaWAN TTN gateway, to a TTN application, then onto some other cloud service, somehow getting to HomeKit (probably via Home Assistant, but details).

Controllers used:
Arduino MKR1310 WAN
CubeCell something

LoRaWAN Gateway:
MikroTik wAP Lora9 kit

Now following along with various guides, getting ther gateway set up wasn't so bad. https://help.mikrotik.com/docs/display/ROS/The+Things+Stack was ok. Do need to ensure the correct channels are selected (in my case, AU915 Sub 2) and also "Network Servers" is correct prior to enabling the device. This caught me out I think because I wasn't saving it correctly, and still seems to be flakey. Do double check values. https://community.element14.com/challenges-projects/design-challenges/save-the-bees-design-challenge/b/blog/posts/beewatch-lorawan-environmental-monitoring-blog-4-lorawan-gateway-set-up-mkr-wan-1310-quirks is also a helpful blog post.

Once the gateway is connected (you can see it's connected in the TTN console), it's time to move onto the controller.

Here are some CubeCell instructions: https://docs.heltec.org/en/node/asr650x/htcc_ab01/lorawan/config_parameter.html
Here is the Arduino guide: https://docs.arduino.cc/tutorials/mkr-wan-1300/the-things-network/

Easy. Except:
1. Do not use AppKey as "00000000000000000000000000000000" - for some reason this fails somewhere. https://descartes.co.uk/CreateEUIKey.html helped.
2. AU915 requires channel masking

MKRWAN needs to call `modem.sendMask("ff000000f000ffff00020000");` prior to the join() call. Foe the CubeCell, I referred to this issue: https://github.com/HelTecAutomation/CubeCell-Arduino/issues/19#issuecomment-581036098 and don't have a very elegant solution yet.

Now that I have connectivity and some basic payload parsing, I can move onto the next steps.

Given the rectriction on cloud processing (30" per month) and Downlink messages (max 10 per day), next steps are to figure out the best way to keep state up to date within these restrictions.
