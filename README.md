# openHAB-Steam-HTC-Vive
Using openHAB, the [Exec Binding](https://www.openhab.org/addons/bindings/exec/) and [Exec Action](https://www.openhab.org/docs/configuration/actions.html#exec-actions) to run [Steam](https://store.steampowered.com/) applications for the [HTC Vive](https://www.vive.com/) on a remote windows computer using the [PsTools](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) and an [OpenSSH-Server](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=gui). Additionally I use the [Network Binding](https://www.openhab.org/addons/bindings/network/) that I can power on an external computer via `Wake on LAN`.

## Preparation

Please make sure that the virtual machine has an `ssh server` because you have to connect to this virtual machine and run a Python code. For Windows please install the [OpenSSH Server](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=gui) as optional feature. On Windows you have also to install the [PsTools](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec).

A tip from me is, to install [Git](https://git-scm.com/downloads) on Windows. Then add `C:\Program Files\Git\usr\bin` and `C:\Program Files\Git\cmd` to your `PATH` variable. This allows you to run commands like `nano` or `cat`, which makes using `SSH` much more convenient.

## Explanation

There are different possibilities to run a steam game from the command line or when you use `runas`:

```
steam steam://rungameid/<game_id>
steam://run/<game_id>
Steam.exe -login <steam_user> <steam_password> -applaunch <game_id>
```

Because we want to run it from an remote computer we have to choose the last possibility. You can get the `<game_id>` from the [https://store.steampowered.com/app/](https://store.steampowered.com/app/).

As example you search for `Google Earth VR`. You will receive following `URL`:

```
https://store.steampowered.com/app/348250/Google_Earth_VR/
```

Then you know that the `<game_id>` is `348250`.

This mean the `URL` is structured as follows:

```
https://store.steampowered.com/app/<game_id>/<game_title>/
```

## Things

The thing `network:pingdevice:vivepc` will be used by the [Network Binding](https://www.openhab.org/addons/bindings/network/). You have to replace `<ip>` with the ip address of your computer. Also you have to replace `<mac>` with the mac address of your computer. Please make sure that you activate the [Wake on LAN behavoior](https://docs.microsoft.com/en-US/troubleshoot/windows-client/deployment/wake-on-lan-feature). Maybe you have also to change a setting on the network card to enable `Wake on LAN (WoL)`.

The thing `exec:command:vivepc:reboot` is used by the [Exec Binding](https://www.openhab.org/addons/bindings/exec/) for restarting the computer. Therefore you have to change `<user>` with the username of your computer and `<password>` that you can login to this user with your password. 

```
Thing network:pingdevice:vivepc   "Network Device: VivePC"   [ hostname="<ip>", macAddress="<mac>", retry=1, timeout=5000, refreshInterval=60000 ]
Thing exec:command:vivepc:reboot [ command="/usr/bin/sshpass -p <password> /usr/bin/ssh -t -o StrictHostKeyChecking=no <user>@<ip> 'shutdown -r'", autorun=false ]
```

## exec.whitelist

Please add to `/etc/openhab/misc/exec.whitelist` following:

```
sudo /usr/sbin/etherwake -i <ethernet_gateway> <mac>
/usr/bin/sshpass -p <password> /usr/bin/ssh -t -o StrictHostKeyChecking=no <user>@<ip> 'shutdown -s'
/usr/bin/sshpass -p <password> /usr/bin/ssh -t -o StrictHostKeyChecking=no <user>@<ip> 'shutdown -r'
/usr/bin/sshpass -p <password> /usr/bin/ssh -t -o StrictHostKeyChecking=no <user>@<ip> 'shutdown -f'
/usr/bin/sshpass -p <password> /usr/bin/ssh -t -o StrictHostKeyChecking=no <user>@<ip> 'shutdown -a'

/usr/bin/sshpass -p <password> /usr/bin/ssh -t -o StrictHostKeyChecking=no <user>@<ip> '"C:\Program Files\PSTools\PsExec.exe" -i 0 "C:\Program Files (x86)\Steam\Steam.exe" -login <steam_user> <steam_password>'
/usr/bin/sshpass -p <password> /usr/bin/ssh -t -o StrictHostKeyChecking=no <user>@<ip> '"C:\Program Files\PSTools\PsExec.exe" -i 0 "C:\Program Files (x86)\Steam\Steam.exe" -login <steam_user> <steam_password> -applaunch 348250'
/usr/bin/sshpass -p <password> /usr/bin/ssh -t -o StrictHostKeyChecking=no <user>@<ip> '"C:\Program Files\PSTools\PsExec.exe" -i 0 "C:\Program Files (x86)\Steam\Steam.exe" -login <steam_user> <steam_password> -applaunch 250820'
/usr/bin/sshpass -p <password> /usr/bin/ssh -t -o StrictHostKeyChecking=no <user>@<ip> '"C:\Program Files\PSTools\PsExec.exe" -i 0 "C:\Program Files (x86)\Steam\Steam.exe" -login <steam_user> <steam_password> -applaunch 348250'
/usr/bin/sshpass -p <password> /usr/bin/ssh -t -o StrictHostKeyChecking=no <user>@<ip> '"C:\Program Files\PSTools\PsExec.exe" -i 0 "C:\Program Files (x86)\Steam\Steam.exe" -login <steam_user> <steam_password> -applaunch 450390'
/usr/bin/sshpass -p <password> /usr/bin/ssh -t -o StrictHostKeyChecking=no <user>@<ip> '"C:\Program Files\PSTools\PsExec.exe" -i 0 "C:\Program Files (x86)\Steam\Steam.exe" -login <steam_user> <steam_password> -applaunch 673070'
/usr/bin/sshpass -p <password> /usr/bin/ssh -t -o StrictHostKeyChecking=no <user>@<ip> '"C:\Program Files\PSTools\PsExec.exe" -i 0 "C:\Program Files (x86)\Steam\Steam.exe" -login <steam_user> <steam_password> -applaunch 620980'
/usr/bin/sshpass -p <password> /usr/bin/ssh -t -o StrictHostKeyChecking=no <user>@<ip> '"C:\Program Files\PSTools\PsExec.exe" -i 0 "C:\Program Files (x86)\Steam\Steam.exe" -login <steam_user> <steam_password> -applaunch 471710'

/usr/bin/sshpass -p <password> /usr/bin/ssh <user>@<ip> '"C:\Program Files\PSTools\PsKill.exe" TheLab.exe'
/usr/bin/sshpass -p <password> /usr/bin/ssh <user>@<ip> '"C:\Program Files\PSTools\PsKill.exe" "The Ranger Lost Tribe Demo.exe"'
/usr/bin/sshpass -p <password> /usr/bin/ssh <user>@<ip> '"C:\Program Files\PSTools\PsKill.exe" RecRoom.exe'
/usr/bin/sshpass -p <password> /usr/bin/ssh <user>@<ip> '"C:\Program Files\PSTools\PsKill.exe" Earth.exe'
/usr/bin/sshpass -p <password> /usr/bin/ssh <user>@<ip> '"C:\Program Files\PSTools\PsKill.exe" vrmonitor.exe'
/usr/bin/sshpass -p <password> /usr/bin/ssh <user>@<ip> '"C:\Program Files\PSTools\PsKill.exe" steam.exe'
```

Please replace `<username>` and `<password>` with the username and password of your computer. Also you have to replace `<ip>` with the ip address of your computer. That the computer can be powered on by `Wake on LAN` you should replace `<ethernet_gateway>` to as example `eth0` or whatever your gateway will be. And you have to change `<mac>` to the mac address of your computer. To login to steam you have to change `<steam_user>` and `<steam_password>` to the username and password of your steam account. And of course if you want to run other games you should change the `<game_id>'s` after `-applaunch`. 

## Items

You have to create following items:

```
Group HTC_Vive "HTC Vive" <projector>

Switch HTC_Vive_Schalter "HTC Vive PC anschalten" (network, HTC_Vive) { channel="network:pingdevice:vivepc:online" }
Switch HTC_Vive_Neustart "HTC Vive PC neustarten" (network, gIoTHTCVive) { channel="exec:command:vivepc:reboot:run" }
Number:Time HTC_Vive_Reaktionszeit "HTC Vive PC Reaktionszeit" (network, gIoTHTCVive) { channel="network:pingdevice:vivepc:latency" }
DateTime HTC_Vive_ZuletztGesehen "HTC Vive zuletzt gesehen" (network, gIoTHTCVive) { channel="network:pingdevice:vivepc:lastseen" }
Switch HTC_Vive_Steam "Steam starten/stoppen" (HTC_Vive)
Switch HTC_Vive_SteamVR "SteamVR starten/stoppen" (HTC_Vive)
Switch HTC_Vive_GoogleEarthVR "Google Earth VR starten/stoppen" (HTC_Vive)
Switch HTC_Vive_TheLab "TheLab starten/stoppen" (HTC_Vive)
Switch HTC_Vive_TheRanger_LostTribe "The Ranger: Lost Tribe starten/stoppen" (HTC_Vive)
Switch HTC_Vive_RecRoom "Rec Room starten/stoppen" (HTC_Vive)
```

## Rules

Following rules will power on the computer with `Wake on LAN` if the computer is not powered on. Starting an steam game means that the rules have to make sure that other steam games are closed. So for each game which will be started steam will be restarted and you have to login to your steam account.

```
rule "Wake on LAN Vive PC"
when
    Item HTC_Vive_Schalter received command ON
then
    val actions = getActions("network", "network:pingdevice:vivepc")
    actions.sendWakeOnLanPacket()
end

rule "Poweroff Vive PC over SSH"
when
    Item HTC_Vive_Schalter changed from ON to OFF
then
    HTC_Vive_Steam.postUpdate(OFF)
    HTC_Vive_SteamVR.postUpdate(OFF)
    HTC_Vive_GoogleEarthVR.postUpdate(OFF)
    HTC_Vive_TheLab.postUpdate(OFF)
    HTC_Vive_TheRanger_LostTribe.postUpdate(OFF)
    HTC_Vive_RecRoom.postUpdate(OFF)
    executeCommandLine("/usr/bin/sshpass","-p","<password>","/usr/bin/ssh","-t","-o","StrictHostKeyChecking=no","<user>@<ip>","shutdown","-s")

    gIoT_HTCVive.members.forEach[ item |
        if (item.name != "HTC_Vive_Neustart") {
            item.sendCommand(OFF)
        }
    ]
end

rule "Switch Reboot Vive PC off"
when
    Item HTC_Vive_Neustart changed to ON
then
    HTC_Vive_Schalter.postUpdate(OFF)

    createTimer(now.plusNanos(10000000), [ |
        HTC_Vive_Neustart.sendCommand(OFF)
    ])
end

rule "Vive PC Steam starten"
when
    Item HTC_Vive_Steam changed from OFF to ON
then
    if (HTC_Vive_Schalter.state == "OFF") {
        HTC_Vive_Schalter.sendCommand(ON)
        Thread::sleep(25000)  // wait 25 seconds
    }

    executeCommandLine("/usr/bin/sshpass","-p","<password>","/usr/bin/ssh","-t","-o","StrictHostKeyChecking=no","<user>@<ip>","\"C:\\Program Files\\PSTools\\PsExec.exe\"","-i","0","\"C:\\Program Files (x86)\\Steam\\Steam.exe\"","-login","<steam_user>","<steam_password>")
    Thread::sleep(1000)
end

rule "Vive PC Steam stoppen"
when
    Item HTC_Vive_Steam changed from ON to OFF
then
    HTC_Vive_SteamVR.postUpdate(OFF)
    HTC_Vive_GoogleEarthVR.postUpdate(OFF)
    HTC_Vive_TheLab.postUpdate(OFF)
    HTC_Vive_TheRanger_LostTribe.postUpdate(OFF)
    HTC_Vive_RecRoom.postUpdate(OFF)

    Thread::sleep(1000)
    executeCommandLine("/usr/bin/sshpass","-p","<password>","/usr/bin/ssh","<user>@<ip>","\"C:\\Program Files\\PSTools\\PsKill.exe\"","steam.exe")
end

rule "Vive PC SteamVR starten"
when
    Item HTC_Vive_SteamVR changed from OFF to ON
then
    if (HTC_Vive_Schalter.state == "OFF") {
        HTC_Vive_Schalter.sendCommand(ON)
        Thread::sleep(25000)  // wait 25 seconds
    }

    executeCommandLine("/usr/bin/sshpass","-p","<password>","/usr/bin/ssh","-t","-o","StrictHostKeyChecking=no","<user>@<ip>","\"C:\\Program Files\\PSTools\\PsExec.exe\"","-i","0","\"C:\\Program Files (x86)\\Steam\\Steam.exe\"","-login","<steam_user>","<steam_password>","-applaunch","250820")
    Thread::sleep(1000)
end

rule "Vive PC SteamVR stoppen"
when
    Item HTC_Vive_SteamVR changed from ON to OFF
then
    executeCommandLine("/usr/bin/sshpass","-p","<password>","/usr/bin/ssh","<user>@<ip>","\"C:\\Program Files\\PSTools\\PsKill.exe\"","vrmonitor.exe")
    Thread::sleep(1000)
    HTC_Vive_Steam.sendCommand(OFF)
end

rule "Vive PC Google Earth VR starten"
when
    Item HTC_Vive_GoogleEarthVR changed from OFF to ON
then
    if (HTC_Vive_Schalter.state == "OFF") {
        HTC_Vive_Schalter.sendCommand(ON)
        Thread::sleep(25000)  // wait 25 seconds
    }

    if (HTC_Vive_TheLab.state == "ON") {
        HTC_Vive_TheLab.sendCommand(OFF)
        Thread::sleep(1000)
    }

    if (HTC_Vive_TheRanger_LostTribe.state == "ON") {
        HTC_Vive_TheRanger_LostTribe.sendCommand(OFF)
        Thread::sleep(1000)
    }

    if (HTC_Vive_RecRoom.state == "ON") {
        HTC_Vive_RecRoom.sendCommand(OFF)
        Thread::sleep(1000)
    }

    if (HTC_Vive_SteamVR.state == "OFF") {
        HTC_Vive_SteamVR.sendCommand(ON)
        Thread::sleep(1000)
    }

    executeCommandLine("/usr/bin/sshpass","-p","<password>","/usr/bin/ssh","-t","-o","StrictHostKeyChecking=no","<user>@<ip>","\"C:\\Program Files\\PSTools\\PsExec.exe\"","-i","0","\"C:\\Program Files (x86)\\Steam\\Steam.exe\"","-login","<steam_user>","<steam_password>","-applaunch","348250")
    Thread::sleep(1000)
end

rule "Vive PC Google Earth VR stoppen"
when
    Item HTC_Vive_GoogleEarthVR changed from ON to OFF
then
    executeCommandLine("/usr/bin/sshpass","-p","<password>","/usr/bin/ssh","<user>@<ip>","\"C:\\Program Files\\PSTools\\PsKill.exe\"","Earth.exe")
    Thread::sleep(1000)
    HTC_Vive_SteamVR.sendCommand(OFF)
    Thread::sleep(1000)
    HTC_Vive_Steam.sendCommand(OFF)
end

rule "Vive PC TheLab starten"
when
    Item HTC_Vive_TheLab changed from OFF to ON
then
    if (HTC_Vive_Schalter.state == "OFF") {
        HTC_Vive_Schalter.sendCommand(ON)
        Thread::sleep(25000)  // wait 25 seconds
    }

    if (HTC_Vive_GoogleEarthVR.state == "ON") {
        sendCommand(OFF)
        Thread::sleep(1000)
    }

    if (HTC_Vive_TheRanger_LostTribe.state == "ON") {
        sendCommand(OFF)
        Thread::sleep(1000)
    }

    if (HTC_Vive_RecRoom.state == "ON") {
        sendCommand(OFF)
        Thread::sleep(1000)
    }

    if (HTC_Vive_SteamVR.state == "OFF") {
        sendCommand(ON)
        Thread::sleep(1000)
    }

    executeCommandLine("/usr/bin/sshpass","-p","<password>","/usr/bin/ssh","-t","-o","StrictHostKeyChecking=no","<user>@<ip>","\"C:\\Program Files\\PSTools\\PsExec.exe\"","-i","0","\"C:\\Program Files (x86)\\Steam\\Steam.exe\"","-login","<steam_user>","<steam_password>","-applaunch","450390")
    Thread::sleep(1000)
end

rule "Vive PC TheLab stoppen"
when
    Item HTC_Vive_TheLab changed from ON to OFF
then
    executeCommandLine("/usr/bin/sshpass","-p","<password>","/usr/bin/ssh","<user>@<ip>","\"C:\\Program Files\\PSTools\\PsKill.exe\"","TheLab.exe")
    Thread::sleep(1000)
    HTC_Vive_SteamVR.sendCommand(OFF)
    Thread::sleep(1000)
    HTC_Vive_Steam.sendCommand(OFF)
end

rule "Vive PC The Ranger: Lost Tribe starten"
when
    Item HTC_Vive_TheRanger_LostTribe changed from ON to OFF
then
    if (HTC_Vive_Schalter.state == "OFF") {
        HTC_Vive_Schalter.sendCommand(ON)
        Thread::sleep(25000)  // wait 25 seconds
    }

    if (HTC_Vive_GoogleEarthVR.state == "ON") {
        sendCommand(OFF)
        Thread::sleep(1000)
    }

    if (HTC_Vive_TheLab.state == "ON") {
        sendCommand(OFF)
        Thread::sleep(1000)
    }

    if (HTC_Vive_RecRoom.state == "ON") {
        sendCommand(OFF)
        Thread::sleep(1000)
    }

    if (HTC_Vive_SteamVR.state == "OFF") {
        sendCommand(ON)
        Thread::sleep(1000)
    }

    executeCommandLine("/usr/bin/sshpass","-p","<password>","/usr/bin/ssh","-t","-o","StrictHostKeyChecking=no","<user>@<ip>","\"C:\\Program Files\\PSTools\\PsExec.exe\"","-i","0","\"C:\\Program Files (x86)\\Steam\\Steam.exe\"","-login","<steam_user>","<steam_password>","-applaunch","673070")
    Thread::sleep(1000)
end

rule "Vive PC The Ranger: Lost Tribe stoppen"
when
    Item HTC_Vive_TheRanger_LostTribe changed from ON to OFF
then
    executeCommandLine("/usr/bin/sshpass","-p","<password>","/usr/bin/ssh","<user>@<ip>","\"C:\\Program Files\\PSTools\\PsKill.exe\"","\"The Ranger Lost Tribe Demo.exe\"")
    Thread::sleep(1000)
    HTC_Vive_SteamVR.sendCommand(OFF)
    Thread::sleep(1000)
    HTC_Vive_Steam.sendCommand(OFF)
end

rule "Vive PC Rec Room starten"
when
    Item HTC_Vive_RecRoom changed from OFF to ON
then
    if (HTC_Vive_Schalter.state == "OFF") {
        HTC_Vive_Schalter.sendCommand(ON)
        Thread::sleep(25000)  // wait 25 seconds
    }

    if (HTC_Vive_GoogleEarthVR.state == "ON") {
        sendCommand(OFF)
        Thread::sleep(1000)
    }

    if (HTC_Vive_TheLab.state == "ON") {
        sendCommand(OFF)
        Thread::sleep(1000)
    }

    if (HTC_Vive_TheRanger_LostTribe.state == "ON") {
        sendCommand(OFF)
        Thread::sleep(1000)
    }

    if (HTC_Vive_SteamVR.state == "OFF") {
        sendCommand(ON)
        Thread::sleep(1000)
    }

    executeCommandLine("/usr/bin/sshpass","-p","<password>","/usr/bin/ssh","-t","-o","StrictHostKeyChecking=no","<user>@<ip>","\"C:\\Program Files\\PSTools\\PsExec.exe\"","-i","0","\"C:\\Program Files (x86)\\Steam\\Steam.exe\"","-login","<steam_user>","<steam_password>","-applaunch","471710")
    Thread::sleep(1000)
end

rule "Vive PC Rec Room stoppen"
when
    Item HTC_Vive_RecRoom changed from ON to OFF
then
    executeCommandLine("/usr/bin/sshpass","-p","<password>","/usr/bin/ssh","<user>@<ip>","\"C:\\Program Files\\PSTools\\PsKill.exe\"","RecRoom.exe")
    Thread::sleep(1000)
    HTC_Vive_SteamVR.sendCommand(OFF)
    Thread::sleep(1000)
    HTC_Vive_Steam.sendCommand(OFF)
end
```

Please replace `<username>` and `<password>` with the username and password of your computer. Also you have to replace `<ip>` with the ip address of your computer. To login to steam you have to change `<steam_user>` and `<steam_password>` to the username and password of your steam account. And of course if you want to run other games you should change the `<game_id>'s` after `-applaunch`.

## Sitemaps

At least you have to add following to your sitemap:

```
Text label="HTC Vive PC" icon="television" {
    Switch item=HTC_Vive_Schalter label="HTC Vive PC [%s]"
    Switch item=HTC_Vive_Neustart label="HTC Vive PC neustarten"
    Text item=HTC_Vive_Reaktionszeit label="HTC Vive PC Reaktionszeit [%s]"
    Text item=HTC_Vive_ZuletztGesehen label="HTC Vive zuletzt gesehen [%s]"
    Switch item=HTC_Vive_Steam label="Steam starten/stoppen"
    Switch item=HTC_Vive_SteamVR label="SteamVR starten/stoppen"
    Switch item=HTC_Vive_GoogleEarthVR label="Google Earth VR starten/stoppen"
    Switch item=HTC_Vive_TheLab label="TheLab starten/stoppen"
    Switch item=HTC_Vive_TheRanger_LostTribe label="The Ranger: Lost Tribe starten/stoppen"
    Switch item=HTC_Vive_RecRoom label="Rec Room starten/stoppen"
}
```
