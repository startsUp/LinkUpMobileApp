<template>
  <div>
    <q-card class="my-card">
      <q-card-section>
        <q-icon
          class="fixed-right q-ma-md"
          color="red-4"
          size="sm"
          name="cancel"
          clickable
          @click="$root.$emit('closeEventForm')"
        />
        <p style="font-size:35px; margin-top:0;" class="q-my-md text-primary">
          <b>Create Event</b>
        </p>

        <form class="row justify-center">
          <div>
            <q-input
              outlined
              v-model="eventFormData.name"
              class="row q-mt-sm"
              label="Event Name"
              style="width:270px"
            />
          </div>

          <div>
            <q-input outlined style="width:270px" class="q-mt-md" v-model="eventFormData.datetime">
              <template v-slot:prepend>
                <q-icon name="event" class="cursor-pointer">
                  <q-popup-proxy transition-show="scale" transition-hide="scale">
                    <q-date v-model="eventFormData.datetime" mask="YYYY-MM-DD hh:mm a">
                      <div class="row items-center justify-end">
                        <q-btn v-close-popup label="Close" color="primary" flat />
                      </div>
                    </q-date>
                  </q-popup-proxy>
                </q-icon>
              </template>

              <template v-slot:append>
                <q-icon name="access_time" class="cursor-pointer">
                  <q-popup-proxy transition-show="scale" transition-hide="scale">
                    <q-time v-model="eventFormData.datetime" mask="YYYY-MM-DD hh:mm a">
                      <div class="row items-center justify-end">
                        <q-btn v-close-popup label="Close" color="primary" flat />
                      </div>
                    </q-time>
                  </q-popup-proxy>
                </q-icon>
              </template>
            </q-input>
          </div>

          <div class="row">
            <q-select
              style="width:275px"
              class="q-mt-md"
              outlined
              v-model="eventFormData.activity"
              :options="options"
              label="Activity"
            />
          </div>
          <div class="row">
            <q-input
              outlined
              v-model="eventFormData.locationString"
              style="width:275px"
              class="q-mt-md"
            >
              <template v-slot:prepend>
                <q-icon name="location_on" color="primary" />
              </template>
            </q-input>
          </div>

          <div class="row fixed-center q-mt-xl">
            <q-btn
              color="primary"
              rounded
              text-color="white"
              size="18px"
              style="width:120px;"
              @click="submitEventForm"
              label="Create"
            />
          </div>
        </form>
      </q-card-section>
    </q-card>
  </div>
</template>

<script>
const opencage = require("opencage-api-client");
import { GeoPoint } from "boot/firebase";
import { mapActions, mapGetters } from "vuex";
import { date } from "quasar";
 

export default {
    
     mounted(){
         console.log(this.getEventCreated)
         this.eventCreated = this.getEventCreated; 
        
     },
  components: {},
  data() {
    return {
        eventCreated:false,
        eventFormData: {
        name: "",
        datetime: "",
        activity: "",
        location: {
          lat: 0,
          lng: 0,
        },
        locationString: "",
      },
      options: [
        "Soccer",
        "Basketball",
        "Tennis",
        "Badminton",
        "Football",
        "Baseball",
        "Mountain Biking",
        "Yoga",
        "Rock Climbing",
        "Volleyball",
        "Cricket",
        "Cycling",
      ],
    };
  },
  
  methods: {
    ...mapActions("userData", ["addEvent",'setRecentlyAddedEvent','setEventCreated']),
    scrolled(position) {

    },
    submitEventForm() {
      //find location

      var apikey = "78c298a5b92147e094a67c9115a62e98";
      var latitude = "51.0";
      var longitude = "7.0";
      var api_url = "https://api.opencagedata.com/geocode/v1/json";

      var request_url =
        api_url +
        "?" +
        "key=" +
        apikey +
        "&q=" +
        encodeURIComponent(this.eventFormData.locationString) +
        "&pretty=1" +
        "&no_annotations=1";

      // see full list of required and optional parameters:
      // https://opencagedata.com/api#forward

      var request = new XMLHttpRequest();
      request.open("GET", request_url, true);
      request.onload = () => {
        if (request.status == 200) {
          
            this.setEventCreated(true);
            this.$root.$emit('closeEventForm')
            this.eventCreated = this.getEventCreated;
          let data = JSON.parse(request.responseText);
          this.eventFormData.location = new GeoPoint(
            data.results[0].geometry.lat,
            data.results[0].geometry.lng
          );
          // this.eventFormData.datetime = date.extractDate(eventFormData.datetime)
          // console.warn("Extracted Date", date.extractDate(this.eventFormData.datetime, 'YYYY-MM-DD hh:mm a'))
          this.addEvent({...this.eventFormData, datetime: date.extractDate(this.eventFormData.datetime, 'YYYY-MM-DD hh:mm a')});
          this.setRecentlyAddedEvent(this.eventFormData)
        } else if (request.status <= 500) {
          console.log("unable to geocode! Response code: " + request.status);
        } else {
          console.log("server error");
        }
      };

      request.onerror = function () {
        console.log("unable to connect to server");
      };

      request.send(); // make the request
    },
  },
  computed:{
      ...mapGetters('userData',['getEventCreated'])
  }
};
</script>
