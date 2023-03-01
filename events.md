Bump reminder
```js
module.exports = {
  name: "messageCreate",
  once: false,
  async execute(client, message) {
    let embedDescription;
    let msg;
    for (let embed of message.embeds) {
      embedDescription = embed.description;
      msg = message;
    }
    if (
      embedDescription ===
      `Bump done! :thumbsup:
Check it out [on DISBOARD](https://disboard.org/server/1047769639247171584).`
    ) {
      if (msg.interaction.commandName === "bump") {
        message.channel
          .send({
            content: `Thx for bumping our Server! We will remind you in 2 hours!
  <@${msg.interaction.user.id}>`,
          })
          .then((mes) => {
            setTimeout(() => {
              mes.channel.send(`<@${msg.interaction.user.id}> Bump is ready`);
            }, 7.2e6);
          })
          .catch(console.error);
      }
    }
  },
};
```
Boost Event
```js
module.exports = {
  name: "guildMemberUpdate",
  once: false,
  async execute(client, oldM, newM) {
    let oldPremium = oldM.premiumSince;
    let newPremium = newM.premiumSince;
    console.log(oldPremium);
    console.log(newPremium);

    if (!oldPremium && newPremium)
      return newM.guild.channels.cache.get("1064854471806550097").send({
        embeds: [
          {
            description: `**${newM.user}** boosted the server! Thank you!`,
            color: 3092790,
          },
        ],
      });
  },
};
```
vanity
```js
const Discord = require("discord.js");
module.exports = {
  name: "presenceUpdate",
  once: false,
  async execute(client, oldMember, newMember) {
    let role = newMember.guild.roles.cache.get("1062188506526523412");

    // status check
    let regex = new RegExp("/vein")
    let arr = ["discord.gg/vein", "/vein", ".gg/vein"];
    if (
      newMember.activities[0]?.name === "Custom Status" &&
      regex.test(newMember.activities[0]?.state) === true
    ) {
      let user = newMember.guild.members.cache.get(newMember.userId);
      if (!role) return;
      if (!user) return;
      if (user.roles.cache.has(role.id)) return;
      user.roles.add(role).catch((e) => {
        console.log(e);
      });
      let info = new Discord.EmbedBuilder()
        .setDescription(
          `${user.user} thanks for repping **/vein**`
        )
        .setColor("#2f3136");
      let msg = await client.channels.cache.get("1058651016771416144").send({
        embeds: [info],
      });
      setTimeout(() => {
        msg.delete().catch(e => { })
      }, 5000)
    }

    if (
      newMember.activities.length === 0 ||
      newMember.activities[0]?.name === "‎" ||
      newMember.activities[0]?.state === null ||
      !newMember.activities[0]?.name === "Custom Status" ||
      (newMember.activities[0]?.name === "Custom Status" &&
        regex.test(newMember.activities[0]?.state) === false)
    ) {
      let user = newMember.guild.members.cache.get(newMember.userId);
      if (!role) return;
      if (!user) return;
      if (!user.roles.cache.has(role.id)) return;
      user.roles.remove(role).catch((e) => {
        console.log(e);
      });
    }
  },
};
```
