# Bot-
Bot mp 
#!/usr/bin/env python3
# coding: utf-8

import irclib
import ircbot
import time

TEAM = "#Salon"


class BotModeration(ircbot.SingleServerIRCBot):
    def __init__(self):
        """
        Constructeur qui pourrait prendre des paramètres dans un "vrai" programme.
        """
        nickname = "Pseudo"
        realname = "Ceque tu veux"
        ircbot.SingleServerIRCBot.__init__(self, [("irc.test.org", 6667, "TonMDP")],
                                           nickname, realname)
        self.insultes = ["con", "pute"] # Liste à agrandir pour un "vrai" programme.
        self.uselessmsgs = ["cc", "ca va", "sa va"]

    def _log(self, target=None, msg=None, serv=None):
    	"""
    	Log un message dans un fichier situé dans le dossier log/
    	Exemple sur #test:
    	<Testeur> 123 test
    	Dans le fichier log/#test.log il y aura:
    	[07/03/17 00:25:07] <Testeur> 123 test
    	"""
        if msg in self.uselessmsgs :
            pass
        
    	elif target is not None:
    		if target.lower() not in ["#Salon".lower(), TEAM.lower()]:
    			serv.privmsg(TEAM, "%s: %s" % (target, msg))
    			
    	else:
    		serv.privmsg(TEAM, "%s" % msg)

    def on_welcome(self, serv, ev):
        """
        Méthode appelée une fois connecté et identifié.
        Notez qu'on ne peut rejoindre les canaux auparavant.
        """
#        serv.privmsg("NickServ", "register TonMDP TonM@il")
        channel = "#Salon"
        serv.join(channel)
        serv.join(TEAM)
        serv.join("#Salon")
		
    def on_privmsg(self, serv, event):
        nickname = irclib.nm_to_n(event.source())
        channel = event.target()
        message = event.arguments()[0]
        if nickname == "Pseudo":

            if message.startswith("!say "):
                data = message[5:]
                tab = data.split(" ")
                if len(data) < 2:
                    serv.privmsg(nickname, "!say <channel> <message>")
                else:
                    channel = tab[0]
                    text = data[len(channel) + 1:]
                    
                    serv.privmsg(channel, text)
        self._log(nickname, "<%s> %s" % ("Me dit en pv", message), serv)

    def on_nick(self, serv, event):

        oldNickname = irclib.nm_to_n(event.source())
        newNickname = event.target()
        
        self._log(msg="%s a changé son pseudonyme en %s" % (oldNickname, newNickname), serv=serv)			

    def on_kick(self, serv, event):
    	nickname = irclib.nm_to_n(event.source())
    	channel = event.target()
    	
    	if len(event.arguments()) == 1:
    		self._log(channel, "%s a été kick" % (nickname), serv)
    	else:
    		self._log(channel, "%s a kick : %s pour la raison suivante : %s" % (nickname, event.arguments()[0], event.arguments()[1]), serv)
		
if __name__ == "__main__":
BotModeration().start()
