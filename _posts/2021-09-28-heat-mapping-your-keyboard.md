---
layout: post
title: Heat Mapping Your Keyboard
category: Keyboard
tags: [keyboard]
---

Having full control over your keyboard layout as outlined in [this]({% post_url 2018-03-26-you-should-customize-your-keyboard %}) previous post is very powerful. But it also puts the onus on you to ensure that the keys are used efficiently, and hopefully ergonomically. Customizing the layout can feel somewhat intuitive for where to put keys based on usage, but there is still _some_ emergent behavior that arises as the keyboard is used in practice that might lead to unexpected or surprising findings about the layout.

# Why Map Key Presses
The best way to determine the efficiency of a keyboard layout is to log the key presses while using the keyboard normally and plot those keys as a heat map to visually see where the most key presses occur.
This will help find two potential issues with the layout:

1. Regularly used keys that are far away from the home row/neutral typing position. These keys should be moved closer to the home row to increase speed and reduce finger traveling.
2. Rarely used keys that are in the home row or near the neutral typing position. These keys are taking up precious space that could be better used by other keys that are farther away.

# Example Heat Map Analysis
Gathering a heat map from a QMK-powered keyboard can be done quite easily using the steps outlined in [this link](https://precondition.github.io/qmk-heatmap) and the corresponding heat map tool. Note that it is important to gather enough key presses to get an accurate representation of normal typing behavior/keyboard usage. Too little input could result in a misrepresentation of key usages because it can over-emphasize edge cases that happened to come up in higher frequency during the test.

Below is an example heat map using the above tool against a few days of normal keyboard usage. It is much more typing/text focused than coding-focused. Depending on keyboard usage, it could be worthwhile to run multiple heat map tests against different use cases, or ensure that the single heat map accurately represents the usage-split - if the keyboard is normally used for 50% coding, the heat map should have roughly half of its data from coding sessions.

{% include carousel.html height="50" unit="%" data=site.data.carousel-heat-map %}

The above images show a split of the heat map - overall, then a heat map for each individual layer.
Analyzing the heat map can be done in two separate parts: the alpha keys and the rest of the keyboard.

## Key Analysis
Overall, the key presses track very well with the home row. This can generally be attributed to the outer keys in both the main area and the thumb cluster being hard to reach and thus leaving those keys as unmapped, or only used for rare key functions.

One of the major changes that helped bring keys closer to the home row was to use the raise/lower/adjust pattern from the Planck keyboard. This allows for the thumbs to adjust between the four layers, and thus provide four distinct possibilities of home row configurations, or eight if you consider the two sides independently.

Also, the heat map highlights the benefit of the DVORAK alpha key layout - it re-arranges the keys from the QWERTY design to pull the most-used keys to the home row. This is one of the main benefits to [switching to an alternative layout]({% post_url 2016-01-01-switching-to-programmers-dvorak %}).

The only issue that sticks out in this heat map is in the 3rd layer - a single key is used that is off of the home row. This key and the key next to it are new additions to the board to delete a whole word either forward or backward. These can easily be moved to unused home row keys.