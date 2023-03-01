Leaderboard
```js
if (message.content.toLowerCase().startsWith("?leaderboard")) {
    let data = await db.list();
    let bank = await db.list();
    let arr = [];
    for (i = 0; i < data.length; i++) {
      let balance = await db.get(data[i]);
      let key = data[i].slice(7);
      arr.push({ id: key, bal: balance });
    }

    let sorted = arr.sort((a, b) => b.bal - a.bal);
    let mapped = await Promise.all(
      sorted.map(async (l, i) => {
        let user = await client.users.fetch(`${l.id}`);
        return `\`(#${i + 1})\` • ${user?.username || "User not found"} • ${
          l.bal
        }$`;
      })
    );

  let test = mapped.filter(value => {
    return value.includes(message.author.username)
  })

    console.log(test);
    
    const ordinal = (i) => {
      const j = i % 10;
			const k = i % 100;
			if (j === 1 && k !== 11) return `${i}st`;
			if (j === 2 && k !== 12) return `${i}nd`;
			if (j === 3 && k !== 13) return `${i}rd`;
			return `${i}th`;
		}
      
    let place = {
        ord: ordinal(Number(test[0]?.match(/`\(#(\d+)\)`/)[1])),
        num: Number(test[0]?.match(/`\(#(\d+)\)`/)[1]),
      };

    let leaderEmbed = new Discord.MessageEmbed()
      .setTitle(`LeaderBoard`)
      .setDescription(mapped.slice(0, 10).join("\n"))
      .setColor("GREEN")
      .setFooter({
        text: `You are ${isNaN(place.num) ? 'not in the leaderboard' : `in ${place.ord} place`}`
			});
    message.channel.send({ embeds: [leaderEmbed] });
  }
  ```
  Balance
  ```js
  if (message.content.toLowerCase().startsWith("?balance")) {
    let user = message.mentions.users.first() || message.author;
    let balance = await db.get(`wallet_${user.id}`);
    let bank = await db.get(`bank_${user.id}`);

    if (balance === null) balance = 0;
    if (bank === null) bank = 0;
    let currency = "💵";
    let moneyEmbed = new Discord.MessageEmbed()
      .setTitle(`${user.username}'s Balance`)
      .setDescription(
        `Wallet: ${balance} ${currency}\nBank: ${bank} ${currency}`
      )
      .setColor("GREEN")
      .setThumbnail(user.displayAvatarURL({ dynamic: true }));
    message.channel.send({ embeds: [moneyEmbed] });
  }
  ```
  AddBalance
  ```js
  if (
    message.content.toLowerCase().startsWith("?addbalance") &&
    message.author.id === "your_id"
  ) {
    let user = message.mentions.users.first();
    if (!user) return message.reply(`Provide an user`);
    let amount = message.content.trim().split(/ +/g)[2];
    console.log(amount);
    if (!amount) return message.reply(`Provide an amount`);

    amount = Number(amount);
    let currentBalance = await db.get(`wallet:${user.id}`);
    if (currentBalance === null || isNaN(currentBalance) === true)
      currentBalance = 0;
    currentBalance = Number(currentBalance);
    message.channel.send(`Sent ${amount} To wallet`);
    let reward = (currentBalance += amount);
    console.log(currentBalance);
    await db.set(`wallet_${user.id}`, reward);
  }
});
```
