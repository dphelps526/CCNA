# Various Quality of Life Settings

## Commands Used

* `hostname SW1` - Sets the hostname to SW1
* `no ip domain-lookup` - IOS tries to use DNS name resolution which may be annoying if you mistype a command. This will disable that but will also prevent you from telneting using a hostname.
* `exec-timeout 0 0` - Allows you to set the length of the inactivity timer. 0 mins and 0 secs means "never time out".
* `logging synchronous` - Will only display syslog messages at convenient times. For instance, it won't print a syslog message in the middle of a show command.
* `history size 20` - Sets the number of commands saved in the buffer.

## Configurations

The following commands can be used in the lab to increase productivity.

``` bash
Press RETURN to get started.

Switch>enable
Switch#configure terminal 
Enter configuration commands, one per line.  End with CNTL/Z.

Switch(config)#hostname SW1
SW1(config)#no ip domain-lookup

! Console settings
SW1(config)#line console 0
SW1(config-line)#exec-timeout 0 0
SW1(config-line)#logging synchronous 
SW1(config-line)#history size 20

! Telnet or SSH settings
SW1(config-line)#line vty 0 15
SW1(config-line)#exec-timeout 0 0
SW1(config-line)#logging synchronous 
SW1(config-line)#history size 20
SW1(config-line)#end

SW1#wr
```


