<template>
  
  <div class="container has-text-centered">
    <div>{{message}}</div>
    <a>
      <section id="zero" @click="publish('zero')" class="hero is-primary is-bold">
        <div class="hero-body">
        
            <div class="container">
              <h1 class="title">0</h1>
            </div>
        </div>
      </section>
    </a>
    <a>
      <section id="one" @click="publish('one')" class="hero is-warning is-bold">
        <div class="hero-body">
          <div class="container">
            <h1 class="title">1</h1>
          </div>
        </div>
      </section>
    </a>
</div>
  
</template>

<script defer>
export default {
  name: 'Controller',
  data () {
    let clientID = "clientID_" + parseInt(Math.random() * 100);
    return {
      client: new Paho.MQTT.Client('broker.hivemq.com', 8000, clientID),
      topic: 'World',
      message : "Connecting to MQTT..."
    }
  },
  methods: {
    onConnectionLost : function(responseObject){  
      console.log("onConnectionLost:"+responseObject.errorMessage);
      this.message = "Lost Connection"
    },
    onMessageArrived : function (message) {
      console.log("onMessageArrived:"+message.payloadString);
    },
    onConnect:function onConnect() {
      // Once a connection has been made, make a subscription and send a message.
      console.log("onConnect");
      this.message = "Connected"
      this.client.subscribe(this.topic);
      let message = new Paho.MQTT.Message("Hello");
      message.destinationName = "World";
      this.client.send(message);
    },
    publish: function (val)  {
        console.log(`publish ${val}`)
        let message = new Paho.MQTT.Message(val);
        message.destinationName = "World";
        message.qos = 0;
        this.client.send(message);
    }
  },
  mounted: function() {
    this.client.connect({
      onSuccess: this.onConnect
    //   userName: 'user',
    //   password: 'pass'
    });
    this.client.onConnectionLost = this.onConnectionLost;
    this.client.onMessageArrived = this.onMessageArrived;
  }
}

</script>

<style>
section {
  min-height:50vh;
}

h1.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 12rem;
  color: #35495e;
  letter-spacing: 1px;
}
</style>
<!-- <style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style> 
-->
