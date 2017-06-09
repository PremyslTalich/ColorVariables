# ColorVariables
ColorVariables moves setting the colors in chat messages from plugin developers to server masters. It allows server masters to set colors for all plugins (with ColorVariables) in one simple step.
For example, changing chat prefix color requires to edit just one line in `colorvariables/global.cfg` config. And it works for one or all plugins with ColorVariables.

**In layman's terms:** Plugin developers are not supposed to use actual colors, but variables which can be easily set by server masters.

---

### How does it work?

##### Server master:
1. Instal plugin
2. *Optional:* Edit `colorvariables/global.cfg` to set global variables for all plugins with ColorVariables (recommended with first ColorVariables plugin)
3. *Optional:* Edit `colorvariables/plugin.PLUGIN_NAME.cfg` to set plugin's "local" variables as you wish
  - `"{newcolor}" "{darkred}"`
    - **NOT** `"{newcolor}" "darkred"`
  - `"{newcolor}" "{#FF0000}"`
    - **NOT** `"{newcolor}" "#FF0000"`

##### Plugin developer:
1. Create a plugin with ColorVariables
2. Use color variables in chat messages and not actual colors
3. *Optional:* If you need some new variables, create them with `CAddVariable`
4. *Optional:* If you want, you can create a forwarded variable for other plugins to use (like `{@isRebel CLIENT_INDEX}`, `{@isZombie CLIENT_INDEX}`, `{@rainbow}` etc.)
5. *Optional:* If you want to use variables from other plugins, load them with `CLoadPluginConfig` or `CLoadPluginVariables` (If server master renamed plugin binary, this won't work!)

**Quick example:**<br/>
Normal: `PrintToChatAll("\x02[prefix]\x01 %N gave you \x03AK-47\x01!", client)`<br/>
CV: `CPrintToChatAll("%N gave you {highlight}AK-47{default}!", client)`<br/>


**ColorVariables plugin example:**
```SourcePawn
#include <colorvariables>

public OnPluginStart()
{
	CSetPrefix("KillInfo"); // Set plugin chat prefix to "KillInfo" (will be used in every print function)

	HookEvent("player_death", Event_PlayerDeath);
}

public Action:Event_PlayerDeath(Handle:event, const String:name[], bool:dontBroadcast) 
{
	new victim = GetClientOfUserId(GetEventInt(event, "userid"));
	new attacker = GetClientOfUserId(GetEventInt(event, "attacker"));
	
	CPrintToChatAll("{player %d}%N {default} killed {player %d}%N", attacker, attacker, victim, victim);
	// {prefix}[KillInfo] {teamcolor}Will {default}killed {teamcolor}Mark
	
	CPrintToChat(victim, "{highlight}Player {player %d}%N {highlight}killed you!", attacker, attacker);
	// {prefix}[KillInfo] {highlight}Player {teamcolor}Will {highlight}killed you!
	
	return Plugin_Continue; 
}

```

### Configs & variables loading order

1. Predefined variables (viz. [Predefined variables](#predefined_engine-variables))
2. `colorvariables/global.cfg`
  - Global config loaded by every plugin
  - Cannot contain redirects outside of `global.cfg` or predefined variables
3. `colorvariables/plugin.PLUGIN_NAME.cfg`
  - `CAddVariable` and `CSavePrefix` write to this config
  - Can be loaded by 3rd party plugins with `CLoadPluginConfig` or `CLoadPluginVariables`
4. Optional: `CAddVariable`, `CLoadPluginConfig` and `CLoadPluginVariables`

Newer variables declarations overwrites the older ones.



### Redirecting variables

- Every variable can be redirected to another (except direct colors and forwarded variables)
  - `"mycolor" "{nicecolor}"`
- Variable can be redirected 10x at max


### Direct colors

- Starts with `#`
- Has two different "mods", RGB `{#RRGGBB}` and RGBA `{#RRGGBBAA}`
- Works only in older versions of engine (CS:S, TF2, HL2, DOD:S, ...) - **NOT CS:GO!**
- Of course, direct color cannot be redirected


### Forwarded variables - *"Clever" variables*

- Name starts with `@` and cannot contain whitespace
- Can pass a single argument `{@playerteam 15}` separated by a whitespace
  - `PrintToChatAll("Player {@playerteam %d}%N {defalut}has died!", client, client);`
- Won't work if their parental plugin is not loaded
- For parental plugin:
  - Use forward `public COnForwardedVariable(String:sCode[], String:sData[], iDataSize, String:sColor[], iColorSize)` to catch your variable
  - Must return actual color code - cannot be redirected (use `CGetColor` to get actual color code)


### Chat prefix

- No prefix by default, could be set with `CSetPrefix`
- Works in `CPrintToChat`, `CPrintToChatAll`, `CPrintToChatTeam`, `CPrintToChatAdmins` and `CReplyToCommand`
- Message format: `"{prefix}[Prefix] {default}Message"` (`{reply2cmd}` in case of `CReplyToCommand`)
- Could be turned off for the next use with `CSkipNextPrefix`
- Could be set saved to plugin config file with `CSavePrefix` (other plugins can load it via `CLoadPluginConfig`)


### Message author

- No author by default
- Works in `CPrintToChat`, `CPrintToChatAll`, `CPrintToChatTeam` and `CPrintToChatAdmins`
- Must by set with `CSetNextAuthor` to use in next message

### <a name="predefined_engine-variables"></a>Predefined & Engine variables
Variable         | Usage
---------------- | -------------
`{prefix}`       | Chat prefix color
`{default}`      | Chat message color
`{reply2cmd}`    | Reply to command message color
`{showactivity}` | Show activity message color
---------------- | -------------
`{error}`        | Error content color
`{highlight}`    | Highlighted content color
`{player}`       | Highlight player in message (could be used as `{player CLIENT_INDEX}` to get a player's team color)
`{settings}`     | Settings content color
`{command}`      | Highlight command in message
---------------- | -------------
`{team0}`        | Spectator color
`{team1}`        | Terrorists, Red team color
`{team2}`        | Counter-Terrorists, Blue team color

- Older engine versions (CS:S, TF2, HL2, DOD:S, ...)
  - [Dr. McKay's More Colors](https://www.doctormckay.com/morecolors.php)
- Newer engine versions (CS:GO, ...)
  - ![CS:GO colors](https://raw.githubusercontent.com/KissLick/ColorVariables/master/csgo%20colors.png)
- Engine colors (usability depends on engine version)
  - `{engine 1}`, `{engine 2}`, `{engine 3}`, ..., `{engine 16}`
