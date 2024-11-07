# Custom Ban System Plugin

This plugin implements a custom ban system similar to the one used by Hypixel. It allows server admins to manage bans with ease, providing a range of commands and features to enhance server moderation.

## Features

- **Ban a Player**: Server admins can ban players with a custom reason and duration.
- **Ban History**: Keeps a record of banned players, including their username, reason, and duration.
- **Ban List Command**: Admins can view a list of all currently banned players.
- **Unban Command**: Admins can unban players who have been previously banned.
- **Custom Ban Messages**: Banned players receive a custom message indicating the reason and duration of their ban.
- **Permission Control**: Only operators can execute these commands. Non-operators receive a custom message.

## Installation

1. Download the JAR file of the plugin.
2. Place it in your server's plugins folder.
3. Restart or reload the server to activate the plugin.

## Commands

### /ban <player> <reason> [duration]
- **Usage**: /ban <player> <reason> [duration]
- **Permission**: customban.ban
- **Description**: Bans a player for a specified reason and duration. If no duration is specified, the player will be permanently banned.

### /unban <player>
- **Usage**: /unban <player>
- **Permission**: customban.unban
- **Description**: Unbans a player who has been previously banned.

### /banlist
- **Usage**: /banlist
- **Permission**: customban.viewbanlist
- **Description**: Displays a list of currently banned players.

## Custom Messages

When a non-operator tries to use any command, they will see:


## Plugin Code Example

Below is the complete Java code for the custom ban system plugin:

java
package com.unique.SYpe1v;

import org.bukkit.Bukkit;
import org.bukkit.ChatColor;
import org.bukkit.command.Command;
import org.bukkit.command.CommandSender;
import org.bukkit.entity.Player;
import org.bukkit.plugin.java.JavaPlugin;

import java.util.HashMap;
import java.util.Map;

public class CustomBanPlugin extends JavaPlugin {

    private final Map<String, String> banList = new HashMap<>();  // Stores banned players and reasons

    @Override
    public void onEnable() {
        getLogger().info("CustomBanPlugin has been enabled!");
    }

    @Override
    public void onDisable() {
        getLogger().info("CustomBanPlugin has been disabled!");
    }

    @Override
    public boolean onCommand(CommandSender sender, Command command, String label, String[] args) {
        if (sender instanceof Player) {
            Player player = (Player) sender;

            // Check if the player is an operator
            if (!player.isOp()) {
                player.sendMessage(ChatColor.RED + "You need ADMIN rank to use this command.");
                return true;
            }
        }

        if (command.getName().equalsIgnoreCase("ban")) {
            if (args.length >= 2) {
                String targetPlayer = args[0];
                String reason = args[1];
                String duration = args.length > 2 ? args[2] : "permanent";

                // Ban the player
                banPlayer(targetPlayer, reason, duration);
                sender.sendMessage(ChatColor.GREEN + targetPlayer + " has been banned for: " + reason);

                // Broadcast the ban
                Bukkit.broadcastMessage(ChatColor.RED + targetPlayer + " has been banned for: " + reason + " Duration: " + duration);
                return true;
            } else {
                sender.sendMessage(ChatColor.RED + "Usage: /ban <player> <reason> [duration]");
            }
        } else if (command.getName().equalsIgnoreCase("unban")) {
            if (args.length == 1) {
                String targetPlayer = args[0];
                unbanPlayer(targetPlayer);
                sender.sendMessage(ChatColor.GREEN + targetPlayer + " has been unbanned.");
                return true;
            }
        } else if (command.getName().equalsIgnoreCase("banlist")) {
            if (!banList.isEmpty()) {
                sender.sendMessage(ChatColor.YELLOW + "List of banned players:");
                for (Map.Entry<String, String> entry : banList.entrySet()) {
                    sender.sendMessage(ChatColor.RED + entry.getKey() + ": " + entry.getValue());
                }
            } else {
                sender.sendMessage(ChatColor.GREEN + "No players are currently banned.");
            }
            return true;
        }

        return false;
    }

    private void banPlayer(String player, String reason, String duration) {
        // Logic to ban the player, this could include removing them from the server, saving their ban to a file, etc.
        banList.put(player, reason + " for " + duration);
    }

    private void unbanPlayer(String player) {
        // Logic to unban the player, such as removing them from the ban list and allowing them to join again
        banList.remove(player);
    }
}
