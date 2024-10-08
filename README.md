# my powershell scripts

## Fix mouse pointer on a Windows guest in QEMU with NVIDIA GPU

The problem: spice uses virtual GPU to render the mouse poiner when the guest is accessed through virt-viewer. This causes the mouse pointer to become invisible when a guest is using NVIDIA GPU as a passthrough PCIE device. Re-enabling NVIDIA GPU within a running guest causes the mouse pointer to be visible again. This problem does not occur when accessing the guest through RDP/parsec/sunshine.

Get the name of the GPU device in the guest:

```
wmic path win32_VideoController get name
```

Create the powershell script `notepad reenable.ps1`:

```
get-pnpdevice | where {$_.friendlyname -like "NVIDIA GeForce RTX 4050 Laptop GPU"}| disable-pnpdevice -Confirm:$false
get-pnpdevice | where {$_.friendlyname -like "NVIDIA GeForce RTX 4050 Laptop GPU"}| enable-pnpdevice -Confirm:$false
```

Then run the script using the command in the terminal:

```
powershell -Command "Start-Process powershell \"-ExecutionPolicy Bypass -NoProfile -NoExit -Command `\"cd \`\"C:\Users\user\%`\"; & \`\".\reenable.ps1\`\"`\"\" -Verb RunAs"
```

or put the command in a .cmd file in `%userprofile%\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup`

The script will ask for admin rights, use keyboard to navigate to "Yes" and press Enter.
