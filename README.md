# Play Tzar on Linux with Bottles

## Intro ⚔️

Sometimes you feel like playing a game from the past just to realize it is 2026, the game is **Tzar: Burden of the Crown (2000)**, and you've forgone Windows for Linux quite a while ago. But despair not!

I tried to make it work with Steam, Wine, Lutris + game-specific scripts, with an old installer and fresh GOG purchase... To no good effect. So I solved it with Bottles wine manager and lived to tell the tale, more specifically to share the ancient spells so you too can:

- Play Tzar on Linux
- At full-screen with high resolution
- With sound effects and MIDI music intact
- Easily launchable via a script

Enjoy the experience! 


### Environment

This solution will work on many systems, but for the sake of reproducibility: I've run it on Linux Mint 22.3 (based Ubuntu 24.04.3 LTS), with [Bottles](https://usebottles.com/) 61.1.

### Requirements

We will use:
- **Bottles** installed via Flatpak (for example through Mint's software manager), 
- **Tzar** installer file, in my case: `setup_tzar_2.0.0.8.exe`,
- **HD patch v3.4** downloaded from [here](https://www.moddb.com/games/tzar-the-burden-of-the-crown/downloads/tzar-burden-of-the-crown-hd-patch-1280x720-1920x1200-ver-30)

## The Alchemy 📜

### 1: Create a New Bottle

1. Open Bottles
2. Click "Create New Bottle"
3. Configure:
   - Name: `Tzar` (or any other)
   - Environment: `Gaming`
   - Runner: default (`soda-9.0-1`)

### 2: Install Dependencies

The game requires specific Windows libraries, as it was built with older Microsoft development tools. You could download them online, individually, and place at appropriate paths, but the easiest option is to enter your newly crated `Tzar` bottle, navigate to **"Dependencies"**, then search and install:

1) `vcredist6sp6` (Visual C++ 6 Service Pack 6) - provides the `mfc42.dll` library required to run Setup.exe. If you search for `mfc42`, you will find "it", but `mfc42u.dll` will be installed, which is not identical.

2) `vcredist2015` (Visual C++ 2015) - provides the `mcf140.dll` library required by the `HD Patch` that we'll install later. If your game already includes the patch, this step is probably still needed.


### 3: Install the Game

1. In your `Tzar` bottle, click **"Run Executable"**
2. Navigate to and run the Tzar installer file (e.g., `setup_tzar_2.0.0.8`). 
3. Accept the default settings. 

When the installer finishes and tries to launch the game, it will show an error: "Invalid TZAR configuration detected. Running configuration program". The visible cursor will probably be misaligned with the image so you'll need to "hunt" to click "OK" and leave - it is all fine, we will fix it. If you installed dependencies correctly, you will see a configuration window. Go to step 4.

**A note on file paths:**

The installation path will be `C:\GOG Games\Tzar - The Burden of the Crown`, which will actually install to: `/home/[username]/.var/app/com.usebottles.bottles/data/bottles/bottles/Tzar2/drive_c/GOG Games/Tzar - The Burden of the Crown/` on linux. When you click "Launch executable" within your Bottle, in the default directory you will see paths such as: `drive_c/...` and `dosdevices/c:/...`, both leading to the game folder. The former is the actual Windows C: drive folder, and the latter is a shortcut pointing at it. Always navigate through **`drive_c`** to avoid extra errors.

### 4: Run Initial Setup

In case the config program did not run by itself, do this:

1. Click **"Run Executable"** → browse to `drive_c/GOG Games/Tzar - The Burden of the Crown/`
2. Find and run `Setup.exe` (not Tzar.exe)

You can adjust the settings as desired, but might notice there are only two resolutions available, the sound test works and the music doesn't. When you launch the game from there, you will see the main menu, but the game will be small, aligned to the screen corner and misaligned with the cursor again.

**Notes:**

If, for some reason, you need to run the game in a non-full-screen mode aleady, in your bottle go to Settings/Advanced Display Settings/ and enable `Virtual Desktop`. Now go back to "Run Executable", and navigate to `Tzar.exe`. The game will be in a small window, which makes scrolling the map awkward, but functional and with cursor aligned.

If for some reason one of the executables doesn't work, next to `Run Executable` click the cog to modify the launch options, and select `Run in Terminal`. After launching the program you will see an additional window - the terminal - with a log of errors like potentially missing libraries.

### 5: Install the HD Patch v3.4

The original game maxes out at 1024x768 resolution, does not display on screen centre and misaligns with the cursor. The HD Patch scales the UI to higher resolutions and fixes these issues.

1. Download [here](https://www.moddb.com/games/tzar-the-burden-of-the-crown/downloads/tzar-burden-of-the-crown-hd-patch-1280x720-1920x1200-ver-30)
2. Extract - unzip the downloaded file
3. Whether in the file manager or in the bottle "Browse" navigate to the `/Tzar - The Burden of the Crown/` folder, and copy **all extracted files and folders** directly into this game folder.
4. In Bottles, choose `Run Executable` and navigate to `TzarSettings.exe`
5. In the settings window: select your monitor's native resolution, click **Save and Run Tzar**

The game should now launch in a fully functional full screen mode! Test and exit the game. If needed, go back to Settings. Keep in mind that in the original `Setup.exe` menu, the resolution has to stay set to `1024x768` irrespectively of our config in `TzarSettings.exe`.

### 6: Running the Game

I your bottle, click `Add shortcut` and navigate to (newly created) `TzarRunner.exe` (not the original `Tzar.exe`) - this is how we will be launching the game from now. 

### 7: Installing a MIDI Synthesizer

The game uses `.wav` files for environment sounds, which works fine, and `MIDI` files for music, which requires additional setup here. We'll use Timidity++ as a software MIDI synthesizer with an optional high-quality soundfont. Install `timidity` from your software manager, or through the terminal:

```bash
sudo apt install timidity
```

### 8 Optional: Install a HQ Soundfont

By default, timidity will use a basic set of recorded instrument samples. To improve on that, download sth like [GeneralUser GS soundfont](http://www.schristiancollins.com/generaluser.php) and extract the zip file. There, you will find a file like `GeneralUser-GS.sf2`. Let's save it in a better place:

```bash
# Create soundfonts directory if it doesn't exist
sudo mkdir -p /usr/share/sounds/sf2/
   
# Move the soundfont (adjust path to your Downloads folder)
sudo mv ~/Downloads/GeneralUser-GS.sf2 /usr/share/sounds/sf2/
```

### 9: Configure Game for Music

1. Start Timidity in a terminal (keep this terminal open). 

Basic:
```bash
timidity -iA
```

OR with the new soundfont:
```bash
timidity -iA -x "soundfont /usr/share/sounds/sf2/GeneralUser-GS.sf2"
```
   
You should see:
```
TiMidity starting in ALSA server mode
Opening sequencer port: 128:0 128:1 128:2 128:3
```

2. In Bottles, run `Setup.exe` from the game folder. Click "Test Music". 

It should play now. Don't click "Run Tzar" as it will not use "TzarRunner.exe" - exit instead. 

The music is now set up. But keep expectations low. You may notice slight scratches or artifacts in the MIDI playback, or the music may even get stuck and stop at times. This is a known issue with Wine + Timidity + PipeWire and unfortunately difficult to eliminate. A less elegant, but better sounding solution is to just play the songs in a music player, manually, outside the bottle. To each their own.


### 10: Create Automated Launch Script

You can manually start Timidity from a terminal and then the game from Bottles every time. But you can also automate it. Bottles provides an option to provide a pre-run and post-run scripts, but... These run in a Flatpak sandbox, while Timidity must run outside to access audio devices. So we create an external launch script instead.

1. Open a text editor and create a new script file:

```bash
nano ~/launch-tzar.sh
```

2. Paste this content and save the file:

```bash
#!/bin/bash

# Start Timidity with custom soundfont (or remove the -x "...")
timidity -iA -x "soundfont /usr/share/sounds/sf2/GeneralUser-GS.sf2" &
TIMIDITY_PID=$!

# Wait a moment for Timidity to initialize
sleep 1

# Launch game through Bottles CLI
flatpak run --command=bottles-cli com.usebottles.bottles run -b Tzar -e "C:\\GOG Games\\Tzar - The Burden of the Crown\\TzarRunner.exe"

# Kill Timidity when game closes
kill $TIMIDITY_PID
```

3. Make the fle executable:

```bash
chmod +x ~/launch-tzar.sh
```

4. Test the Launch Script

Double click the script file of lanuch it from the terminal with `~/launch-tzar.sh`. You should see Timidity starting, game launching at full resolution and the music should play once you start the game (there is no music in the menu). Does it work? Congratulate yourself! Occassionally it may stop working. In such case run timidity manually in the terminal and re-do `Setup.exe` until it works. Retro gaming!

## Optional improvements 🪄

This guide is a living document. Some things are yet to be tested or improved - feel free to pick up the torch:

- LAN multiplayer - does it work? How?
- Desktop/start menu shortcut - a clean way to launch without a terminal
- Prepare a Lutris script or export the bottle as `.tar.gz` for easy install
- Alternative configurations for the Bottle, for any experience improvements?
- Make the music setup truely robust? If you're a powerful mage.

If you've improved on this setup, please open a PR or drop an issue and let me know! Let's build on each other's spells. 

## Credits

**HD Patch** by **OLDodin** on ModDB: https://www.moddb.com/games/tzar-the-burden-of-the-crown/downloads/tzar-burden-of-the-crown-hd-patch-1280x720-1920x1200-ver-30

**GeneralUser GS** soundfont by **S. Christian Collins** at http://www.schristiancollins.com/generaluser.php

**Inspiration:**
- MIDI discussion and solution by **4Vertikal4** at https://github.com/Heroic-Games-Launcher/HeroicGamesLauncher/issues/4556 and https://github.com/Heroic-Games-Launcher/HeroicGamesLauncher/issues/4409.
- ProtonDB Tzar reports: https://www.protondb.com/app/825730

**Tools:**
- Bottles: https://usebottles.com/
- Timidity++: http://timidity.sourceforge.net/
