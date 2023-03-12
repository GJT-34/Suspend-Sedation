# Using the Suspend-Sedation Service for Convenience and to Conserve Battery Power

This involves some minor changes to the 'suspend-sedation' systemd service, which is an alternative to the 'suspend-then-hibernate' function. 

This service solved a problem I encountered in which my Dell XPS 9310 (running Ubuntu 22.04) could enter and exit both suspend and hibernate, but would fail to properly execute suspend-then-hibernate. I wanted to use suspend-then-hibernate because it would combine the speed of suspend mode with the power savings of hibernation. My laptop goes into and out of suspend quickly, but suspend will significantly drain my battery if left in that state for an extended period. Hibernation is slower, but saves battery power. (Like many newer laptops, mine does not have a suspend function that equates to 'deep sleep.')

Like suspend-then-hibernate, the service will wake up a laptop out of suspend mode after a set number of seconds--defined by the ALARM_SEC variable in the script--and go into hibernation. In my case, that variable is set to 21600 seconds, which is six hours. The service starts on boot, so it's ready when needed.

I've changed the usual version of this service in two ways. First, I inserted an three-second delay upon coming out of suspend, before going into hibernation. This was needed for the service to work properly on my laptop. Until I added the delay, I had the same problems with this service as I did with suspend-then-hibernate. Presumably, what was happening was that both this service and the suspend-then-hibernate function were asking my laptop to go into hibernation before it had fully come out of suspend. Adding the three-second delay solved for this. Second, I adjusted the service so that upon waking at the alarm it will go back into suspend and not into hibernation if the laptop is plugged in.

There are links with additional background in the script itself.

# How To
- Create a file in /etc/systemd/system/ called suspend-sedation.service. Note that you'll need administrator privileges to do so. Note also that on some systems, the location of systemd service files may be in another directory. (I did this running Ubuntu 22.04.) 
- Copy the contents of the suspend-sedation.service from this repository into the new file.
- Change the number of seconds in the ALARM_SEC variable if you want the laptop to exit suspend and enter hibernation at some point other than the six hour mark. For instance, while testing it out you may wish to set it to 30 seconds, to quickly see if it's working as advertised. 
- Save the file.
- In a terminal, enter the following commands to reload service files, immediately start the new service, check its status, and enable it at system startup:
```
sudo systemctl daemon-reload
sudo systemctl start suspend-sedation.service
sudo systemctl status suspend-sedation.service
sudo systemctl enable suspend-sedation.service
```
