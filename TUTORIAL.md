# Linux Mint: Installing Fcitx5 Input Method

This is a guide to resolve the system freezing issue due to the conflict between the Fcitx and IBus input method when attempting installing Chinese Keyboard.
In default, it is recommended to download Fcitx input method framework. However, when switching the keyboard language after changing Fcitx to the default
input method, the system encounters a compatibility issue on the UI, characterized by system freezing, graphic malfunction, etc. 

## Cause of this issue
This is due to the conflict between the two different input method frameworks. They both register the environment variables, essetially causing the outage of 
the CPU resources.

## Solution
1. Uninstalling IBus Input Method
```
sudo apt remove -- purge ibus ibus-*
```
This step ensures all IBus package are fully uninstalled.

2. Clear Fcitx Input Method
```
sudo apt remove -- purge fcitx fcitx-*
```
This step ensures all Fcitx package are fully uninstalled, preventing any conflicts with IBus method.

3. Install Fcitx5 Input Method via Command
```
sudo apt update
```
```
sudo apt install fcitx5 fcitx5-chinese-addons fcitx5-config-qt
```
This step installs the latest version of fcitx5 framework.

4. Set Default Input Method
```
im-config -n fcitx5
```

5. Set Environment Variables
```
nano ~/.profile
```
This step allows us to customize the environment variables
```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS=@im=fcitx
```
After setting and saving it as out default environment variables, we execute it
```
source ~/.profile
```

6. Lauch Fcitx5 Input Method
```
fcitx5 -d
```
This steps to enable the input method
 
7. Ensure the Input Method Is Running
```
pgrep -a fcitx
```
This checks the state of Fcitx5 input method. On the terminal, it should display '''fcitx5''' and '''fcitx5-dbus'''

8. Adding Chinese Pinyin (Input Method)
```
fcitx5-configtool
```
This opens the configuration window of Fcitx5 input method. On the right dropdown window, brose 'Pinyin' and add it
 
9. Testing
If you notice a notification after adding Pinyin for keyboard language, congrats, you have successfully download the
Chinese Pinyin Keyboard. You can now press Ctrl + Space to switch the keyboad!
    

