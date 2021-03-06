Commands in Rocketmod 5 are very similar to RocketMod 4 however the Execute method has been changed.

## Base Command

```csharp
using Rocket.API.Commands;

namespace SamplePlugin
{
	public static SampleCommand : ICommand
	{
		public string Name => "Sample";
		public string [] Aliases => null;
		public string Summary => "My sample plugins sample command.";
		public string Description => null;
		public string Permission => null;
		public string Syntax => "";
		public IChildCommand [] ChildCommands { get; } = null;

		public bool SupportsUser (Type user) => true;

		public void Execute (ICommandContext context)
		{
			context.User.SendMessage ("The sample command says hi!", System.Drawing.Color.Yellow);
		}
	}
}
```

**Name**: 			The commands name (e.g. `rocket`, `buy`, etc)
**Aliases**:		A list of alternative for the name 
**Summary**:		A short summary of the commands function
**Description**:	A long description entailing the commands functionality, Not Required
**Permission**:		The permission the player needs to use the command. Recommended to keep null as it defaults to PluginName.CommandName
**Syntax**:			A syntax string for the command `[]` = *optional* and `<>` = *required* (e.g. [steamId] <itemId>)
**ChildCommands**:	A list of child commands (e.g. `shop sell`, `shop buy` : `buy` and `sell` are child commands of `shop`)
**SupportsUser**:	A bool to check if a user passed through is supported for this command
**Execute**:		Where your code gets executed

## Examples
### Get Parameters

```csharp
public void Execute (ICommandContext context)
{
	// Get ulong at index 0
	if (context.Parameters.TryGet<ulong> (0, out ulong steamId))
		throw new CommandWrongUsageException ("Missing SteamID Parameter");
}
```

### Get UnturnedPlayer from User

> **WARNING:** Only using this if Supports is set to `typeof (UnturnedUser).IsAssignableFrom (user)` or add checks to see if `context.User is UnturnedUser`

```csharp
public void Execute (ICommandContext context)
{
	UnturnedPlayer player = ((UnturnedUser) context.User).Player;
}
```

### Add Child Command
First create a command that inherits from IChildCommand (it has same structure as ICommand) 

```csharp
public static SampleJokeCommand : IChildCommand
{
	public string Name => "Joke";
	public string [] Aliases => null;
	public string Summary => "Tells a joke.";
	public string Description => null;
	public string Permission => null;
	public string Syntax => "";
	public IChildCommand [] ChildCommands { get; } = null;
	
	public bool SupportsUser (Type user) => true;
	
	public void Execute (ICommandContext context)
	{
		context.User.SendMessage ("<insert generic joke here>", System.Drawing.Color.Yellow);
	}
}
```

And then in your base command set ChildCommands to this

```csharp
public IChildCommand [] ChildCommands { get; } = new IChildCommand []
{
	new SampleJokeCommand ()
};
```

Now `/sample joke` should work.

### Run method in Plugin
Just add the following to your SampleCommand

```csharp
private MyPlugin plugin;

public SampleCommand (IPlugin plugin)
{
	this.plugin = (MyPlugin)plugin; 
}
```
