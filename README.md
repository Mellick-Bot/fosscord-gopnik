# fosscord-gopnik

This package guerilla-patches the [fosscord.js](https://github.com/discordjs/fosscord.js) library in order to allow it to connect to [Fosscord](https://github.com/fosscord/fosscord-server) instances.

# Install
```
npm install fosscord-gopnik --save
```

# Usage
Exactly as you would the standard fosscord.js package, but instead just import this one.
```ts
import Fosscord from "fosscord-gopnik";
const client = new Fosscord.Client({
	intents: ["GUILD_MESSAGES"],

	// New default endpoints:
	http: {
		api: 'https://discord.com/api',
		cdn: 'https://cdn.discordapp.com',
		invite: 'https://discord.gg',
	},
});

client.on("ready", () => {
	console.log(`Logged in as ${client.user?.tag}`);
})

client.on("messageCreate", (msg) => {
	console.log(msg);
})

client.login("token here");	//Token from instance
```

# Instancing ( WIP )
Allows connections to multiple Fosscord/Discord instances. Using mostly the same API as fosscord.js Shards.

```ts
/* entry point */
import { InstanceManager } from "fosscord-gopnik";

const manager = new InstanceMananger("./bot", {
	instances: [
		{
			token: "bot token for this instance",
			baseUrl: "url of fosscord/discord instance",
			// rather than baseUrl, you may alternatively provide:
			http: {
				api: "fosscord api endpoint",
				cdn: "cdn endpoint",
				invite: "invite endpoint",
			}
		},
		{ ... }
	]
});

// Or:
manager.createChild({ token: "", baseUrl: "", });

manager.on("childCreate", (child) => {
	console.log(`Created instance ${child.instance.baseUrl}`);
});

/* bot.ts */
import Fosscord from "fosscord-gopnik";
const Client = new Fosscord.Client({ ... });

//later:
if (client.instanced) {		//Similar to client.shard
	client.instanced.broadcastEval((client) => console.log("Hi!"));
	client.instanced.fetchClientValues("guilds.cache.size")
		.then(x => console.log(`I am in ${x.reduce((c, p) => c + p)} guilds over all my instances`));
	client.instanced.respawnAll();
	client.instanced.respawn();
	client.instanced.send(message);

	await client.instanced.instanceIdsForGuildId(message.guild.id);	//returns guild, child id and instance id ( currently instance baseUrl )
	await client.instanced.instanceIdsForUserId(message.user.id);	//see above

}

//INSTANCE_TOKEN is set by InstanceMananger for this process.
client.login(process.env.INSTANCE_TOKEN);
```

