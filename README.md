#ColorVariables
ColorVariables moves setting the colors in chat messages from plugin developers to server masters. It allows server masters to set colors for all plugins (with ColorVariables) in one simple step.
For example, changing chat prefix color requires to edit just one line in `colorvariables/global.cfg` config. And it works for one or all plugins with ColorVariables.

---

###How does it work?

#####Server master:
1. Instal plugin
2. Optional: Edit `colorvariables/global.cfg` to set global variables for all plugins with ColorVariables (recommended with first ColorVariables plugin)
3. Optional: Edit `colorvariables/plugin.PLUGIN_NAME.cfg` to set plugin's "local" variables as you wish

#####Plugin developer:
1. Create a plugin with ColorVariables
2. Use color variables in chat messages and not actual colors
3. If you need some new variables, create them with `CAddVariable`
4. If you want, you can create a forwarded variable for other plugins to use (like `{isRebel CLIENT_INDEX}`, `{isZombie CLIENT_INDEX}`, `{rainbow}` etc.)
5. If you want to use variables from other plugins, load them with `CLoadPluginConfig` or `CLoadPluginVariables` (If server master renamed plugin binary, this won't work!)

**Examples:**
Normal: `PrintToChatAll("\x02[prefix]\x01 %N gave you \x03AK-47\x01!", client)`
CV: `CPrintToChatAll("%N gave you {highlight}AK-47{default}!", client)`
