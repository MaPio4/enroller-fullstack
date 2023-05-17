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
    addNewMeetingRequest(meeting) {
      axios.post('/api/meetings', meeting)
        .then(response => {
            console.log("[OK] ADD MEETING");
            meeting.id = response.data.id;
            this.addNewMeeting(meeting);            
        })
        .catch(response => {    
          console.log("[ERROR] ADD MEETING");
        });
    },
    addNewMeeting(meeting) {      
      this.meetings.push(meeting);
    },
    addMeetingParticipantRequest(meeting) {
      axios.post(`/api/meetings/${meeting.id}/participants`, this.user)
        .then(response => {
            console.log(`[OK] ADD PARTICIPANT, MEETING ID:${meeting.id}`);            
            this.addMeetingParticipant(meeting, this.user);            
            return;
        })
        .catch(response => {    
          console.log(`[ERROR] ADD PARTICIPANT, MEETING ID:${meeting.id}`);
        });      
    },
    addMeetingParticipant(meeting, user) {
      meeting.participants.push(user.login);
    },
    removeMeetingParticipantRequest(meeting, user) {
      axios.delete(`/api/meetings/${meeting.id}/participants/${this.user.login}`)
        .then(response => {
            console.log(`[OK] DELETE PARTICIPANT, MEETING ID:${meeting.id}`);            
            this.removeMeetingParticipant(meeting, this.user);            
            return;
        })
        .catch(response => {    
          console.log(`[ERROR] ADELETE PARTICIPANT, MEETING ID:${meeting.id}`);
        }); 
    },
    removeMeetingParticipant(meeting, user) {
      meeting.participants.splice(meeting.participants.indexOf(user.login), 1);
    },
    deleteMeetingRequest(meeting) {
      axios.delete(`/api/meetings/${meeting.id}`)
        .then(response => {
            console.log(`[OK] DELETE MEETING, MEETING ID:${meeting.id}`);            
            this.meetings.splice(this.meetings.indexOf(meeting), 1);            
            return;
        })
        .catch(response => {    
          console.log(`[ERROR] DELETE MEETING, MEETING ID:${meeting.id}`);
        }); 
      
    },
    deleteMeeting(meeting) {
      this.meetings.splice(this.meetings.indexOf(meeting), 1);
    },
  }
}
</script>
