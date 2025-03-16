---
title: Omron D6F-P
created: 2025-03-16 17:04
tags: []
---

# Omron D6F-P

An analog [[00aa Analog flow sensors|flow sensor]].

There are two main variants:
- 0 - 0.1 l/min
- 0 - 1 l/min

Given the general [[ISO 9053-1 Requirements]], both can be a reasonable option based on the specimen surface area of choice.

Both variants can be bought for approx. €40 at [Mouser](https://cz.mouser.com/c/?marcom=115753066) and probably also elsewhere.

Note that such sensor needs to be paired with a decent [[04aa Analog to digital conversion|ADC]].

## Output voltages

![[Attachments/Omron D6F-P output voltages.png]]

## Main characteristics

![[Attachments/Omron D6F-P characteristics.png]]

The choice of flow sensor depends on the dimensions (The target flow speed across the specimen is 0.5 mm/s, while the sensors are typically volumetric or mass based). For square samples, the target flow rate would be:
- 102 mm side - 0.3 l/min
- 45 mm side - 0.06 l/min
Given the 5% FS accuracy, the 0-1 l/min variant gives the error of $\pm 0.05 \;\mathrm{l/min}$, which is already probably too much for the smaller mentioned sample size. On the other hand, the measurement can consist of many sequential takes at different flow rates (as implemented in the [flowres](https://github.com/vyhyb/flowres/tree/main) package), which gives a more valuable information about the sample behavior. Based on such argument, the 0-1 l/min variant is probably more suited.

## Datasheet

[[Attachments/datasheets/en_d6f_series-1128136.pdf|Datasheet]]

