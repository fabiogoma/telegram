import groovy.json.*

pipeline {
    agent any
    stages {
        stage('Reply latest message sent to Bender on telegram') {
          steps{
            script{
              // Load properties
              Properties properties = loadProperties()

              String baseURL = "https://api.telegram.org/bot" + properties.token + "/"

              // Enable or disable proxy
              enableProxy(properties.proxy_enabled.toBoolean())

              // Testing 
              if (getMessageText(lastUpdate(baseURL))){
                sendMessage(baseURL,getChatID(lastUpdate(baseURL)),"test")
              }
            }
          }
        }
    }
}

def loadProperties(){
  // Load properties from a configuration file
  Properties prop = new Properties()
  try { 
    File propertiesFile = new File('config.properties')
    propertiesFile.withInputStream {
      prop.load(it)
    }
  } catch (ex) {
    File propertiesFile = new File('/tmp/default.properties')
    propertiesFile.withInputStream {
      prop.load(it)
    }
  }
  return prop
}

def enableProxy(isEnabled){
  if (isEnabled){
    System.getProperties().put("proxySet", "true");
    System.getProperties().put("proxyHost", "localhost");
    System.getProperties().put("proxyPort", "3128");
  }
}

// Method to retrieve chat id
def getChatID(update){
  return update.message.chat.id
}

// Method to retrieve message text
def getMessageText(update){
  return update.message.text
}

// Method to retrieve the last message received by the bot
def lastUpdate(url) {
  def getUpdatesURL = url + "getUpdates"
  def getUpdatesConnection = new URL(getUpdatesURL).openConnection()

  def jsonSlurper = new JsonSlurper()
  def response = jsonSlurper.parseText(getUpdatesConnection.content.text) 

  total_updates = response.result.size() - 1

  // get last record message update
  return response.result[total_updates]
}

// Method to reply messages by the bot 
def sendMessage(url, chatID, messageText){
  def sendMessageURL = url + "sendMessage"
  def sendMessageConnection = new URL(sendMessageURL).openConnection();
  def data = '{"chat_id": \"' + chatID + '\", "text": \"' + messageText + '\"}'

  sendMessageConnection.setRequestMethod("POST")
  sendMessageConnection.setDoOutput(true)
  sendMessageConnection.setRequestProperty("Content-Type", "application/json")
  sendMessageConnection.getOutputStream().write(data.getBytes("UTF-8"));

  return sendMessageConnection.getInputStream().getText()
}