# Updating System

## 1.) When first starting the Ubuntu virtual machine I checked to see what updates from the apt package manager can be applied. 
![upgradable](../images/apt%20list%20--upgradable.png)

## 2.) Next I updated and upgraded my Ubuntu install then rebooted the system. 
![update_and_upgrade](../images/update%20and%20upgrade.png)

# User Management

## 1.) I switched to the root user to execute root commands without having to enter my password each time. You can see the terimal changed and has a # instead of a $.
![sudo_su](../images/sudo_su.png)

## 2.) Next, I created two users. One with the useradd command and one with the adduser command.
![add_user](../images/adding_user.png)

As you can see the adduser command has extra functionality and allows for a more complete user setup.

## 3.) I switched to sally as the user. 
![sally_su](../images/sally_su.png)

Now you can see sally@UbuntuServer-Lab.

## 4.) When trying to add a user as sally permission is denied.
![sally_perm](../images/sally_perm_denied.png)

This is because sally does not have root permissions. 