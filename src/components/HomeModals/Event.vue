<template>
  <q-card class=" bg-accent text-white q-my-md" @click="$root.$emit('closeEventPopup')">
      <q-card-section class="row">
        <div class="text-h6 text-black q-mt-sm" >{{eventData.name}}</div>
         <q-card-actions>
         <q-chip color="secondary" >{{eventData.activity}}</q-chip>
          
      </q-card-actions>
        <div >
            <div class="col q-mt-sm">
                  <q-btn flat color="black" size='15px' icon="group" ><span class='q-ml-sm'>{{membersNum}}</span></q-btn>
                     <q-btn flat color="black" no-caps size="15px" icon="history" ><span class='q-ml-sm'>{{date}}</span></q-btn>
                      <q-btn  v-if="notJoinedEvents" rounded color="primary" no-caps size="17px" style='margin-right:10px; margin-top:20px' class="q-px-md q-mt-sm absolute-top-right" @click="joinLinkUp" label="Join"></q-btn>
                        <q-btn v-else rounded color="primary" no-caps size="17px" style='margin-right:10px; margin-top:20px' class="q-px-md q-mt-sm absolute-top-right"   @click="getInfo" label="Info"></q-btn>
                      
            </div>
              <q-btn flat color="black" no-caps size='15px' icon="location_on" ><span  class='q-ml-sm '>{{eventData.locationString}} </span></q-btn>
           </div>
              
      </q-card-section>
      
    </q-card>
</template>

<script>
import {date} from  'quasar'
import {mapActions} from 'vuex'
export default {
name:'Event',
mounted(){
  console.log(this.eventData)
},
props:['eventData','notJoinedEvents'],
methods:{
  ...mapActions('userData',['joinEvent']),
  joinLinkUp(){
    this.joinEvent(this.eventData)
    this.$root.$emit('openEventDetailsContainer',this.eventData)
    this.$root.$emit('eventMembers',this.membersNum)
  },
  getInfo(){
   
    this.$root.$emit('openEventDetailsContainer',this.eventData)
    this.$root.$emit('eventMembers',this.membersNum)
  }
},
computed:{
  date(){
    let timeStamp = this.eventData.datetime;
let formattedTime = date.formatDate(timeStamp.toDate(), 'hh:mm a');
let formattedDate = date.formatDate(timeStamp.toDate(), 'MMM:D');
return formattedDate +' @ '+ formattedTime
    
  },
  membersNum(){
    return Math.floor(Math.random() * Math.floor(10));
  }
}
}
</script>

<style>

</style>