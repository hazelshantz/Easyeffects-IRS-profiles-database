# Easyeffects-IRS-profiles-database
## A community maintained database of IRS+easyeffects profiles files, to be used with the convolver

On this repository, you'll find a database with IRS (Impulse Response Sound) files, and easyeffects profiles.


## Background:
I started this project, after noticing that , for some reason, my laptop speakers had better bass and mid frequencies sound with windows, than with Arch Linux. I though it was my speakers, but I was wrong.

So, I started researching the reason, and ended with https://github.com/shuhaowu/linux-thinkpad-speaker-improvements repo, which helped me to fix and match my audio to the way Windows was doing it. 
After trial and error, I was able to create a proper json profile for Easyeffects, that now any other user can load.

Now, I want you, the one reading this repo, to help me profile every possible device, so new users can just get the files and enjoy better audio.

## Notes:
The IRS files were made from Windows, and may be possible to be done from macOS too, with the goal in mind of capturing the way those OS do the audio. by using the convolver, with an IRS file, you can replicate the sound that you had with Windows/macOS, on your Linux system.

This project is meant to be a collab with the Linux community, so you don't have a RAW, flat audio output, by having users uploading their own IRS + Easyeffects profiles.

## How to use:

Download the files matching your laptop/PC model, and place the json on `~/.config/easyeffects/output`, and the `.irs` on `~/.config/easyeffects/irs`

Open EasyEffects, and load the profile for your laptop/PC.
Notice the audio quality improving.

For fine tuning, check that the convolver is using the right `.irs` file.



## Contributing:

I would love anyone on the Linux community, to contribute, and help me grow this repo, into a full database. That way, new Linux users won't need to profile their Windows audio, and just enjoy. 

Of course, if the .irs file doesn't exist, you can create a new one.
To add files to this repo, just create a new folder, and upload your configuration files, with brand and model of your PC/audio device/laptop.

# Creating a new .irs to contribute to this database:

## How to profile Windows audio and get the .irs based on your device:
We need to figure out how the filtering is done with Dolby on Windows. To do this, we can simply play an impulse audio file, and measure the output audio, which is the impulse response and we can use it in a convolution filter. Step by step:
## Note: I recommend you to do this twice or more, depending on how many audio devices you have. 1st, start by only using the speakers of your laptop/PC. then, plug your headphones and repeat the process, and so on.
## This way, you'll end with 2 or more .irs files for each device. Each device has a different wave, so repeat as needed.

1. Install `Audacity` and `VLC` on Windows.
   If you don't have Windows, you can try using [Windows to go](https://www.intowindows.com/rufus-to-create-windows-to-go-usb-drive/) on an external USB drive. You can make a Windows VM, to use rufus for this purpose.
3. Enable all Dolby effects using the Dolby app (`Dolby Audio Premium` at the time of writing this). Convince yourself that it is working by playing some music and turning the Dolby effects on and off.
4. Open `Audacity`, select `Edit` -> `Preferences` and then select the `Audio Settings` on the left tree.
5. Under `Interface` -> `Host`, select `Windows WASAPI`
6. Under `Playback` -> `Device`, select the speakers (`Speaker (Realtek(R) Audio)` for me).
7. Under `Recording` -> `Device`, select the speakers loopback (`Speaker (Realtek(R) Audio) (loopback)` for me).
8. Under `Quality`, make sure the `Project Sample Rate` and `Default Sample Rate` is `48000Hz`.
9. I selected 24-bit for the `Default Sample Format`. 32 bit float might be fine as well.
10. Click `OK` to save the settings.
11. Download the impulse WAV file from either [the original source](https://freesound.org/people/unfa/sounds/205620/) or [a mirror of it in this repo](impulse48khz-2sec.wav).
12. Open the WAV file with VLC. Pause the playback.
13. Go to `Tools` -> `Preferences` in VLC. On the bottom left, it says `Show settings` and there are two radio boxes, `Simple` and `All`. For me, `Simple` is selected, click `All` to get a more detailed preferences menu.
14. On the left tree, go to `Audio` -> `Output modules`.
15. On the right side, the `Audio output module` should be `Windows Multimedia Device output` and the `Media role` should be set to `Video`. I also tried a `Media role` of `Music`, but it made no difference in the impulse response profile on my machine.
16. Click `Save` to save the settings.
17. Go back to Audacity, click the Record button (big circle at the top). The recording might be stuck (no waveform shows on screen), which is OK.
18. Click play in VLC. This should cause the recording to show a waveform in audacity.
19. Stop the recording in Audacity and stop playback in VLC (if necessary).
20. Zoom into the peak of the wavform and see something like the following. You have to zoom in quite far. In the screenshot below, that's a range of abouy 4ms.

<img src="./ImpulseResponseScreenshot.PNG" />

20. Select the audio clip centered around the maximum of the waveform, as shown in the above screenshot. Then go to `File` -> `Export Selected Audio`. Save the file as a WAV file.
21. Rename the resulting file's extension from `.wav` to `.irs`. Keep this file around.

## Importing the .irs file into Easyeffects:

0. Ensure no external speakers are plugged in and this is for the internal
   speakers only. Start playing some music.
1. `sudo apt intall pulseeffects`
2. On the left hand side, activate `Convolver`, on the right panels. There's a
   button that looks like a wave form above `Stereo Width`. Click it.
3. `Import Pulse` and select the desired `.irs` file. Click `Apply`.
4. On the left panel, use the arrow buttons of the `Limiter` row and move it
   below the `Convolver`. Activate that as well.
   - Without doing this, the convolution may cause the audio to clip and you
     may here artifacts due to that.
5. We need to setup a preset so this setup can be turned off when headphones
   are plugged in. On the top right corner, there's a button to set a preset.
   Click on it and a small popup will show with a textbox. Type `Laptop
   Speaker` into it. Press the `+` button. Then press the save button (should
   be the left-most icon button in the row of 3 buttons).
6. Press the middle icon button that looks like an refresh button. This will
   cause this preset to be loaded when the speakers are used.
7. Plug headphones in. Create a new preset called `Headphones` with the top
   right buttons. Switch to the new preset by clicking `Load` next to it.
8. Disable both the `Convolver` and `Limiter` filter.
9. Press save (left icon button) to save to the filter pipeline (which should
   have no filters) to the pipeline.
10. Press the middle icon button to instruct PulseEffects to automatically load
    this preset when headphones are plugged in.
11. Click the hamburger menu at the top right of the screen. Go to `General`
    and click `Start Service at Login`. This makes PulseEffects run on boot.
    - I have seen that I have to reload the filters in PulseEffects after a
      reboot for them to take effect.
12. Optionally could set the `Priority Type` to be `Real Time`. 

Your screen should look something like this (note I edited the Output gain of
the Convolver filter, but that's up to you):

<img src="./easyeffects.png" />

You can test the headphone/laptop speaker switching. My observation is that it
takes about 1-3 seconds for PulseEffects to switch between the presets.

I recommend to add a limiter after the convolver or other effects, so it doesn't sound saturated/distorted. of course, adjust the gain of the effects, and ensure it doesn't peak beyond 0 dB. 

It is recommended to do this on a silent room, with speakers at 70% volume, so you can notice the small differences. 

## Tip:
you can use AutoEQ to get even more accurate sound based on your headphones model.

https://autoeq.app/

Additionally, a more detailed webiste that can be of help:
https://gadgetrytech.squig.link/

# References and documentation

You may like to dig deeper into samplerates and quantum values. here are some documentations

This post can help to calculate the right values:

https://www.reddit.com/r/linux_gaming/comments/1gao420/low_latency_guide_for_linux_using_pipewire/

Official docs can be found here:

https://docs.pipewire.org/page_man_pipewire_1.html

Another useful post regarding values:

https://bbs.archlinux.org/viewtopic.php?id=288886

For a more in depth documentation:
https://wiki.gentoo.org/wiki/PipeWire/en#Configuration_fragments

