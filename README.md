import net.dv8tion.jda.api.JDA;
import net.dv8tion.jda.api.JDABuilder;
import net.dv8tion.jda.api.entities.Member;
import net.dv8tion.jda.api.events.message.MessageReceivedEvent;
import net.dv8tion.jda.api.hooks.ListenerAdapter;

public class ModerationBot extends ListenerAdapter {

    public static void main(String[] args) throws Exception {
        // Replace "YOUR_BOT_TOKEN" with your actual bot token from Discord Developer Portal
        JDA jda = JDABuilder.createDefault("YOUR_BOT_TOKEN").build();
        jda.addEventListener(new ModerationBot());
        jda.awaitReady();
        System.out.println("Bot is online!");
    }

    @Override
    public void onMessageReceived(MessageReceivedEvent event) {
        String message = event.getMessage().getContentRaw();
        String prefix = "!"; // You can customize the command prefix here

        if (event.getAuthor().isBot()) {
            return; // Ignore bot messages
        }

        if (message.startsWith(prefix)) {
            String command = message.substring(prefix.length()).trim();

            if (command.equalsIgnoreCase("kick")) {
                // Implement kick logic here (ensure you have proper permissions)
                String targetUsername = getTargetUsername(message);
                if (targetUsername != null) {
                    Member targetMember = event.getGuild().getMemberByNickname(targetUsername);
                    if (targetMember != null) {
                        // Perform the kick action
                        targetMember.kick().queue();
                        event.getChannel().sendMessage("Successfully kicked " + targetUsername + ".").queue();
                    } else {
                        event.getChannel().sendMessage("Could not find member with username " + targetUsername + ".").queue();
                    }
                } else {
                    event.getChannel().sendMessage("Please specify a valid username to kick.").queue();
                }
            } else if (command.equalsIgnoreCase("ban")) {
                // Implement ban logic here (ensure you have proper permissions)
                String targetUsername = getTargetUsername(message);
                if (targetUsername != null) {
                    Member targetMember = event.getGuild().getMemberByNickname(targetUsername);
                    if (targetMember != null) {
                        // Perform the ban action
                        targetMember.ban(0).queue();
                        event.getChannel().sendMessage("Successfully banned " + targetUsername + ".").queue();
                    } else {
                        event.getChannel().sendMessage("Could not find member with username " + targetUsername + ".").queue();
                    }
                } else {
                    event.getChannel().sendMessage("Please specify a valid username to ban.").queue();
                }
            } else {
                // Add more moderation commands here (e.g., mute, warn)
                event.getChannel().sendMessage("Invalid command. Try '!help' for a list of commands.").queue();
            }
        }
    }

    // Function to extract target username from the message (optional)
    private String getTargetUsername(String message) {
        String[] parts = message.split(" ");
        if (parts.length >= 2) {
            return parts[1];
        } else {
            return null;
        }
    }
}
