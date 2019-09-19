esp-serial-cmdline
==============

Library for [ESP-OPEN-RTOS](https://github.com/SuperHouse/esp-open-rtos)
to communicate with the device with the use of UART serial line

Developer would need to define a callback function to interpret the commands
and supply as parameter to the initialisation function 


```c
// example on_command handler
void on_command_callback(char* _cmd) {
    char *setup_buffer = calloc(128, 1);
    char *ssid = &setup_buffer[0];
    char *password = &setup_buffer[64];

    if( strncmp(_cmd, "setup_wifi", strlen("setup_wifi")) == 0 ) {
	INFO("espresso_switch: setting up wifi with the: %s", _cmd );
	sscanf(_cmd, "%*s %s %s", ssid, password);
	wifi_config_set(ssid, password);
	wifi_config_get(&ssid, &password);
	INFO("espresso_switch: ssid = '%s', password = '%s', restarting...", ssid, password );
    	sdk_system_restart();
    } else if( !strcmp(_cmd, "info_wifi") ) {
	INFO("espresso_switch: getting information about wifi connection");
	wifi_config_get(&ssid, &password);
	INFO("espresso_switch: ssid = '%s', password = '%s'", ssid, password);
    } else if( !strcmp(_cmd, "reset_wifi") ) {
    	INFO("espresso_switch: resetting wifi settings");
    	wifi_config_reset();
    	sdk_system_restart();
    } else if( !strcmp(_cmd, "reboot") ) {
    	INFO("espresso_switch: rebooting");
    	sdk_system_restart();
    } else if( !strcmp(_cmd, "time") ) {
    	INFO("espresso_switch: reporting time of day");
    	time_t ts = time(NULL);
	printf("SYSTEM UTC TIME: %s", ctime(&ts));
    } else if( !strcmp(_cmd, "help") ) {
    	INFO("espresso_switch: Available commands:\n\
    reboot - reboots the device, no changes to the settings\n\
    setup_wifi <ssid> <password> - sets wifi ssid and passwords and reboots \n\
    info_wifi - provides information about current wifi connction \n\
    reset_wifi - resets wifi ssid and password and reboots\n\
    time - report current system time\n\
    }
}

void user_init(void) {
    //initialise command listener with on_command callback
    serial_cmdline_init( on_command ); 
}
```

License
=======

MIT licensed. See the bundled [LICENSE](https://github.com/stellarshenson/esp-serial-cmdline/LICENSE) file for more details.
