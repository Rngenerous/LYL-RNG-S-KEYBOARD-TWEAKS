# LYL RNG'S KEYBOARD TWEAKS (WITH EXPLANATIONS)

---

## 1. The "Data Queue Size" Tweak (Fixes Missed Builds)
By default, Windows allocates a tiny data buffer for keyboard signals. When you are rapidly executing complex mechanical sequences (e.g., executing a double-edit, placing a wall, and pulling out your shotgun in a fraction of a second), Windows can drop or slightly delay a keystroke because the buffer overflowed. 

### How to apply it:
1. Press `Win + R`, type `regedit`, and hit **Enter**.
2. Navigate to: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\kbdclass\Parameters`
3. Look for `KeyboardDataQueueSize`.
4. Double-click it, change the base to **Decimal**, and assure the value is set to 100.

* **Leave "KeyboardDataQueueSize" at Default (100)The Setting: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\kbdclass\Parameters → KeyboardDataQueueSizeThe Value: Leave at 100 (Decimal).The Real Reason: Modern versions of Windows use 100 as the default to handle modern high-speed gaming keyboards (like 1,000Hz to 8,000Hz polling rates). If you lower this to 64 or 50 based on old internet myths, you actually shrink your data buffer by up to 50%. Under heavy game load or intensive endgame scrims, a tiny queue will overflow, causing your keyboard inputs to jam, freeze, or drop completely. Leaving it at 100 ensures your system has a massive, stable runway to process fast sequences cleanly.** *




## 2. The "FilterKeys" Tweak (Removes Key-Repeat Delay)
Windows naturally adds a built-in delay buffer when a key is pressed down or repeated. While standard settings in the Windows Control Panel only allow you to turn this down to a certain point, the Registry allows you to force it to absolute zero. This is crucial for instantly registering consecutive movement taps or keeping turbo-build structures perfectly continuous.

### How to apply it:
1. In the Registry Editor, navigate to: `HKEY_CURRENT_USER\Control Panel\Accessibility\Keyboard Response`
2. Update the following string values exactly as follows:

#### ⏱️ `AutoRepeatDelay = 200` *(or 150 for ultra-fast response - 8 cores reccomended)*
* **The Windows Default (1000):** Windows forces a massive 1-second delay before it registers that a key is being held down (not pressed but held down). yikes ikr. While you won't notice this just running forward, it creates major micro-stalls when you hold a key to pull off consecutive edits, select multiple build pieces, or hold down a weapon slot key.
* **The Tweak (200):** Dropping this value to 200 milliseconds slashes that pause timer by 80%. It makes the transition from your very first tap to a held-down action nearly instantaneous.

⚠️ **Why Not Set It to Zero?**
You might think setting it to 0 would give you the ultimate speed, but never set it to 0. If you drop `AutoRepeatDelay` to 0, Windows completely eliminates the pause timer. The absolute millisecond you touch any key, it will instantly repeat it dozens of times. If you try to tap your wall bind once, you might accidentally place two or three walls, wasting mats. If you try to type a single letter in the game chat or Discord, your screen will look like this: `wwwwwaaaaassssdddd`.

#### 🏎️ `AutoRepeatRate = 12`
Now that you understand the delay timer, `AutoRepeatRate` controls what happens after that timer ends. It dictates how fast Windows repeats the key signal while you continue to hold it down.

When you are executing a fast tunneling sequence or a protected side-jump: You tap your floor bind (`AutoRepeatDelay` ensures it registers instantly). You hold it for a split second to catch your floor grid. `AutoRepeatRate = 12` keeps the placement stream moving perfectly smoothly. It ensures your builds place without a single millisecond of hesitation, but it doesn't flood your CPU with useless duplicate data packets.

* **Zero FPS Drops:** Your CPU handles this clean data speed easily. It prevents the massive input traffic jams that cause micro-stutters during intense endgames.

#### ⚡ `DelayBeforeAcceptance = 0`
This the most important setting in this entire folder for a pro player. It tells Windows to completely turn off its built-in safety filter and register your keypresses instantly. Think of it as the ultimate windows input bypass.
      
* 🛡️ **Why Windows Defaults to 1000:** Out of the box, Windows sets this to 1000 (a full 1-second delay). This is an accessibility feature called "FilterKeys." It is designed for people with hand tremors or typing difficulties. It tells the computer: *"If the user accidentally bumps a key for a split second, ignore it. Only register the key if they hold it down firmly for a full second."* 
* If you leave this at the default setting, Windows is constantly running every single keystroke through a slow "filtering" background check.

#### ⚙️ `Flags = 59`
Normally, Windows measures repeat speeds in sluggish milliseconds. But changing your `Flags` to 59 tells Windows to completely change its math engine. It stops using standard time delays and starts counting in raw engine ticks per second.

In the Windows Registry, the value 59 is a bitmask mathematical configuration. It tells Windows exactly which individual FilterKeys features to turn on or off by combining their assigned numbers.

Windows reads the number 59 as a sum of specific configuration "switches" (bits):1 (FKF_FILTERKEYSON): Turns the FilterKeys system On. 2 (FKF_AVAILABLE): Makes the feature Available to the OS. 8 (FKF_CLICKONKEY): Makes a click sound when a key is pressed (often combined or left as a placeholder flag). 16 (FKF_TWOKEYSOFF): Automatically turns FilterKeys off if two keys are held at once. 32 (FKF_INDICATOR): Shows the FilterKeys Icon on your taskbar.When you add these values together (1 + 2 + 8 + 16 + 32), you get 59.By calculating this exact total, Windows activates the background accessibility system. This system bypasses the default Control Panel limits and forces the OS to read your custom, ultra-fast `AutoRepeatRate` and `AutoRepeatDelay` millisecond parameters directly.

* **Lower Number = Faster & Cleaner:** Setting this tells Windows to execute a tight, hyper-responsive data loop. It matches your hardware's speed perfectly without causing any input lag.
* **The Result:** This completely strips away the standard Windows OS key-repeat latency buffer. The second you tap a key, the command is instantly deployed to the game engine. 

---

## 🛠️ Summary & Performance Note
1. **The Keyboard Tweaks Use Zero CPU Power:** Changing your `KeyboardDataQueueSize` to 64 or 96 simply opens up a wider memory buffer. It does not force your processor to work harder. Your CPU processes keyboard packets in less than a microsecond, so these adjustments take less then 0.0002ms measurable toll on your PC hardware.





---

## 🛑 How to Revert to Factory Windows Defaults

If you ever experience issues or want to restore your computer's original factory keyboard settings, you can create a single-click backup file.

### Option A: The One-Click Restore Script
1. Right-click on your desktop, select **New** > **Text Document**.
2. Copy and paste the exact text block below into the file:

```text
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Control Panel\Accessibility\Keyboard Response]
"AutoRepeatDelay"="1000"
"AutoRepeatRate"="500"
"DelayBeforeAcceptance"="1000"
"Flags"="126"

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\kbdclass\Parameters]
"KeyboardDataQueueSize"=dword:00000032
```

3. Click **File** > **Save As...**
4. Change the *Save as type* dropdown to **All Files (*.*)**.
5. Name the file **`RestoreDefaults.reg`** (ensure it ends in `.reg`) and click Save.
6. Double-click the file on your desktop, accept the Windows security prompt, and **restart your PC**.

### Option B: Manual Revert Checklist
If you prefer to change them back manually inside `regedit`, restore these original default entries (ensure the Base is set to **Decimal** when typing values for `KeyboardDataQueueSize`):

* `AutoRepeatDelay` ➡️ `"1000"`
* `AutoRepeatRate` ➡️ `"500"`
* `DelayBeforeAcceptance` ➡️ `"1000"`
* `Flags` ➡️ `"126 or 114"`
* `KeyboardDataQueueSize` ➡️ `50` *(Displayed as Hexadecimal `32` or Decimal `50`)*


## MADE IN BASH WITH <3 FROM RNG
