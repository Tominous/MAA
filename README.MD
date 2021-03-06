

# Minecraft Advertisement API
## Setup:
Before you even start you will need to setup an Adf.ly account and enable API access. In your IDE add the .jar file to the Build-Path and then in the plugin.yml add `depend: [MAA]`. Copy the MMA.jar file in you plugins folder.

> Attention: On the first load the plugin will automatically disable itself. You need to update the Adf.ly API access info in the config.yml file found in the plugin folder and then specify on which domain the NodeJS is hosted.

Now to NodeJS: open the folder which contains the server and through the command-terminal run `npm install package.json` to install the required libraries.
 Open **index.js** and edit the array named: **allowed_ips**. There you will need to add every IP adress of minecraft server using this API. By default the local IP (127.0.0.1) is already allowed, so if the minecraft-server is hosted on the same machine, you don't have to edit anything.
 To run the server type `node index.js`.

In the NodeJS-server folder, you'll notice there are two .html files present. You may edit them as you wish, but make sure their names don't change (success.html and error.html).

If you've done everything correclty, when you load the NodeJS server and then the minecraft one, everything should work fine. 

## How to use:
Once everything is setup, we can get into coding! 
### Creating a Challenge
To create a "Challenge", or an adf.ly link, you can use following methods:

```java
MAA.add(Player player)
```
This method returns a string:
```java
Player player = Bukkit.getPlayerExact("Exampleplayer");
String link = MAA.add(player);
```

Alternatively you may use this method Asynchronously, adding a CreateCallback() parameter.
Example:
```java
final Player player = Bukkit.getPlayerExact("Exampleplayer");
MAA.add(player, new CreateCallback(){
	public void asyncAddPlayer(String link) {
		//Your code goes in here
	}	
});
```
**Careful**: you can only create one challenge per player!
### Events:
MAA uses the Bukkit Event-API to fetch completed challenges.
To start listening for completed challenges, you will first need to run the `RequestHandler.start()` method and then register a listener for the "ChallengeCompletionEvent":
```java
public class Example implements Listener{
	@EventHandler
	public void onChallengeComplete(ChallengeCompletionEvent e){
		e.getPlayer().sendMessage("You completed the challenge!");
	}
}
```
Then register this listener in you onEnable() method:
```java
onEnable(){
	...//your other code
	Bukkit.getPluginManager().registerEvents(new Example(), this);
	RequestHandler.start();
	...//your other code
}
```

 In case you don't like working with events, you can use the MAA.checkPlayer() method. **CAREFUL:** you should either work with events or with this method, as using both may cause some issues. 
Examples:
```java
	Player player = Bukkit.getPlayerExact("ExamplePlayer");
	boolean bool = MAA.checkPlayer(player);
```
 There is also the async variant:
```java
	final Player player = Bukkit.getPlayerExact("ExamplePlayer");
	MAA.checkPlayer(player, new CheckCallback(){
		public void asyncCheckPlayer(boolean bool){
			//your code goes in here
		}
	});
```
This method returns *false* whenever the challenge has not been completed.
Otherwise, wheter the challenge has been completed or there isn't one, the method will return always *true*.

**Rembember**: the `MAA.checkPlayer()` method, unlike `MAA.hasCompleted()` (which you will learn more about in the next paragraph), always makes an HTTP request to the NodeJS server!

### Has the challenge been completed?
You of course may need to check if the player has completed a challenge, and for that there's the MAA.hasCompleted() method, example:
```java
	Player player = Bukkit.getPlayerExact("ExamplePlayer");
	boolean bool = MAA.hasCompleted(player);
```
This method returns *false* whenever the challenge has not been completed (i.e. while the ChallengeCompletionEvent has not fired for a certain player, after you created a challenge). 
Otherwise, wheter the challenge has been completed or there isn't one, the method will return always *true*.

#### Error handling
I don't I can really call it error handling, but in case something goes wrong in the MAA.addChallenge method, an error-string is returned, there are three types: "Error 1" is for when there's a problem with the NodeJS server; "Error 2" when there's a problem with Adf.ly API and "Error 3" when a challenge already exists for a player.


### Suggestions
I really suggest putting the `MAA.clean()` method in `onDisable()`. This will remove any unused links of created challenges on the Adf.ly website, which otherwise you would have to do on your own.

## Other information
### Example:
Following this [link](https://youtu.be/FjUq4_Bzpd4) you will see an example of how one could use this API. In the [snipplet](https://github.com/hesperiusproject/MAA/tree/master/Snipplet/src) section, you will find the code used in the video.

### Known issues:
I suggest not reloading the plugin once it is enabled on the server for the following reason: allthough the MAA will still work fine, it will start outputting an error on every HTTP request in the console. This bug  is not caused by the plugin itself, but by the libraries it uses.
### Get in touch
If you have a problem or suggestion, you can email my at giovanni@gallmetzer.taa.it or send a PM to my discord **Gio#4621** .
### TO-DO
I plan on creating a maven-repository to make it easier than ever to work with my API!


