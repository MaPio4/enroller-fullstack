<template>
  <div>
    <NewMeetingForm @added="addNewMeetingRequest($event)"></NewMeetingForm>
    <span v-if="meetings.length == 0">Brak zaplanowanych spotkań.</span>
    <h3 v-else>Zaplanowane zajęcia ({{ meetings.length }})</h3>
    <MeetingsList :meetings="meetings"
                  :username="username"
                  @attend="addMeetingParticipantRequest($event)"
                  @unattend="removeMeetingParticipantRequest($event)"
                  @delete="deleteMeetingRequest($event)"></MeetingsList>
  </div>
</template>

<script>
import NewMeetingForm from "./NewMeetingForm";
import MeetingsList from "./MeetingsList";
import axios from "axios";

export default {
  components: {NewMeetingForm, MeetingsList},
  props: {
    username: String, 
    user: Object,   
  },
  data() {
    return {
      meetings: [],      
    };
  },
  methods: {
    allMeetingRequest() {
      axios.get('/api/meetings/all')
        .then(response => {
            console.log("[OK] GET ALL MEETINGS");  
            this.meetings.length = [];            
            let givenMeetings = response.data;           

            for (let i = 0; i < givenMeetings.length; i++) {
              console.log(givenMeetings[i])
              this.meetings.push(givenMeetings[i])
            }                      
        })
        .catch(response => {    
          console.log("[OK] GET ALL MEETINGS");
        });
    },  

    addNewMeetingRequest(meeting) {
      axios.post('/api/meetings', meeting)
        .then(response => {
            console.log("[OK] ADD MEETING");
            this.allMeetingRequest();          
        })
        .catch(response => {    
          console.log("[ERROR] ADD MEETING");
        });
    },

    addMeetingParticipantRequest(meeting) {
      axios.post(`/api/meetings/${meeting.id}/participants`, this.user)
        .then(response => {
            console.log(`[OK] ADD PARTICIPANT, MEETING ID:${meeting.id}`);           
            this.allMeetingRequest();      
            return;
        })
        .catch(response => {    
          console.log(`[ERROR] ADD PARTICIPANT, MEETING ID:${meeting.id}`);
        });      
    },

    removeMeetingParticipantRequest(meeting, user) {
      axios.delete(`/api/meetings/${meeting.id}/participants/${this.user.login}`)
        .then(response => {
            console.log(`[OK] DELETE PARTICIPANT, MEETING ID:${meeting.id}`);            
            this.allMeetingRequest();            
            return;
        })
        .catch(response => {    
          console.log(`[ERROR] ADELETE PARTICIPANT, MEETING ID:${meeting.id}`);
        }); 
    },

    deleteMeetingRequest(meeting) {
      axios.delete(`/api/meetings/${meeting.id}`)
        .then(response => {
            console.log(`[OK] DELETE MEETING, MEETING ID:${meeting.id}`);            
            this.allMeetingRequest();            
            return;
        })
        .catch(response => {    
          console.log(`[ERROR] DELETE MEETING, MEETING ID:${meeting.id}`);
        }); 
    },
  },
  mounted: function () {
        this.$nextTick(function () {
            this.allMeetingRequest();            
        })
  },
}
</script>
