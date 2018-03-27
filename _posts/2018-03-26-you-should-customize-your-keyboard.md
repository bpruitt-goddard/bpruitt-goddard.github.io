---
layout: default
title: You Should Customize Your Keyboard Like You Do Your Workspace
---

Developers and other "professional computer users"' by definition spend the bulk of their time at their computer. So it's not surprising that there are so many guides comparing hardware and software tools to improve productivity. What is surprising though, is the lack of articles about customizing your keyboard experience, since your keyboard is the most used and most important part of the equation. There are guides for choosing a particular model of keyboard, but they focus solely on the hardware aspect and physical layout the the keys. While this is important, it is equally important to consider the ability of the software to tailor the keys to the user. 

Before getting into the benefits of this level of customization, it is important to note the two different levels of customization out there: an abstracted software solution that gives you some amount of flexibility, and the ability to completely modify the firmware directly, giving you complete control. The [Ergodox EZ](https://ergodox-ez.com/) keyboard highlights the difference between these and supports either option.

# Levels of Customization
First is a (typically) simple software abstraction over the keyboard that allows you to make changes in a user-friendly way such as a GUI. The changes are easy to make and/or rollback and provide a good level of customization. They allow keys to be mapped to any character, but usually stop there. For the Ergodox, there is an [online configurator](https://configure.ergodox-ez.com/keyboard_layouts/new) that let's you change any key and create/configure multiple layers (this part is atypical of software solution, but more on it soon).

Second is the more complex case of editing the keyboard's firmware directly. This provides maximum customization, but is offset by the high learning curve. The GUI is replaced with writing the code directly for the firmware. For the Ergodox, you can directly write and flash [QMK firmware](https://qmk.fm/) written in C. There are usually existing layouts to use/copy from at the [GitHub repo](https://github.com/qmk/qmk_firmware), but making your own changes will require a bit of tinkering and compiling the code to flash to your keyboard. 

# Customized Keyboard Benefits
With all of this work, what are the benefits? There are many reasons to change at least a few keys on the keyboard from wanting a different layout (eg Colemak or Dvorak), to personal finger strength/flexibility, to personal usage and some keys being more used in individual workflows than traditional layouts assume. Although this is all possible to do at the operating system level, it becomes more difficult when the keyboard is used across machines, networked to remote machines, or running multiple virtual machines. 

The other major benefit is having distinct "layers" for your keyboard. These can then be switched using a single keypress to completely change all of your keys to something else. An example is switching from alpha keys to the function keys, or a numpad available directly on the home row instead of moving your hand to a dedicated one. Even more advanced usages allow a custom layout for a particular program to map all the shortcuts to single keypresses.

When that is not enough, the complete customizability of the firmware can provide you with additional features and advanced keys. With QMK, there is a [Space Cadet Shift](https://docs.qmk.fm/feature_space_cadet.html) that make the shift keys more versatile by having them enter a left or right paren when tapped and then function as shift when held. Or the [Tap Dance](https://docs.qmk.fm/feature_tap_dance.html) feature that allows the key to function differently depending on how many presses in a row it receives. With manually writing source code for the keyboard, you can have it do virtually anything to speed up your workflow and make you more efficient.

# Conclusion
Being able to tailor the keyboard to the individual user can greatly speed up productivity, especially for shorcut-heavy worfkflows, or those that don't use the standard alpha keys on the keyboard. The standard layout of a keyboard is just that - a standard that works "good enough" for the masses. When diverging from standard usage, your keyboard should be able to diverge and change as well.
