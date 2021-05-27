<script>
import FullCalendar from '@fullcalendar/vue'
import dayGridPlugin from '@fullcalendar/daygrid'
import timeGridPlugin from '@fullcalendar/timegrid'

import { INITIAL_EVENTS, createEventId } from './event-utils'
import resourceTimeGridPlugin from '@fullcalendar/resource-timegrid';
import interactionPlugin from '@fullcalendar/interaction';
import  { Draggable } from '@fullcalendar/interaction';
import swal from 'sweetalert';

export default {
  
  components: {
    FullCalendar // make the <FullCalendar> tag available
  },
   
  data: function() {
    
    return {
      calendarOptions: {
        plugins: [
          dayGridPlugin,
          timeGridPlugin,
          interactionPlugin,
          resourceTimeGridPlugin // needed for dateClick
        ],
        headerToolbar: {
          left: 'prev,next today',
          center: 'title',
          right: 'dayGridMonth,timeGridWeek,timeGridDay'
        },
        initialView: 'resourceTimeGridDay',
        resources: [{"id":"a","title":"1"},{"id":"b","title":"2"},{"id":"c","title":"3"},{"id":"d","title":"4"}],
        initialEvents: INITIAL_EVENTS, // alternatively, use the `events` setting to fetch from a feed
        editable: true,
        selectable: true,
        selectMirror: true,
        dayMaxEvents: true,
        weekends: true,
        select: this.handleDateSelect,
        eventClick: this.handleEventClick,
        eventsSet: this.handleEvents,
        droppable:true,
        
        drop: function(dropInfo) {
      //alert(dropInfo.draggedEl.data + " is dropped at " + dropInfo.date);
      //console.log(dropInfo.draggedEl.innerText + " is dropped at " + dropInfo.date);
      
     console.log(INITIAL_EVENTS);
     if(dropInfo.resource.id==INITIAL_EVENTS[0].resourceId){
          swal( dropInfo.draggedEl.innerText + " is dropped at " + INITIAL_EVENTS[0].title );
     }
     else if(dropInfo.resource.id==INITIAL_EVENTS[1].resourceId) {
        swal( dropInfo.draggedEl.innerText + " is dropped at " + INITIAL_EVENTS[1].title );
     }
     
     
    }
    
        /* you can update a remote database when these fire:
        eventAdd:
        eventChange:
        eventRemove:
        */
       
      },
      currentEvents: []
    }
  },
    mounted(){
      
       let draggableEl = document.getElementById('external-events-list');
            new Draggable(draggableEl, {
      itemSelector: '.fc-event',
      eventData: function(eventEl) {
        return {
          //title: eventEl.innerText.trim()
        };
      }
    });
     let draggableE2 = document.getElementById('external-events-list-2');
            new Draggable(draggableE2, {
      itemSelector: '.fc-event',
      eventData: function(eventEl) {
        return {
          //title: eventEl.innerText.trim()
        };
      }
    });
    },
  methods: {

    handleWeekendsToggle() {
      this.calendarOptions.weekends = !this.calendarOptions.weekends // update a property
    },
      dropremove(arg){
        
        // is the "remove after drop" checkbox checked?
        // if (document.getElementById('drop-remove').checked) {
        //   // if so, remove the element from the "Draggable Events" list
        //   arg.draggedEl.parentNode.removeChild(arg.draggedEl);
        // }
        //console.log(arg)
        console.log(arg.draggedEl.childNodes[1].childNodes[0].data + " is dropped at " + arg.date);
        //console.log(arg.draggedEl.childNodes[1].value);
      },
      
    handleDateSelect(selectInfo) {
      let title = prompt('Please enter a new title for your event')
      let calendarApi = selectInfo.view.calendar

      calendarApi.unselect() // clear date selection

      if (title) {
        calendarApi.addEvent({
          id: createEventId(),
          title,
          start: selectInfo.startStr,
          end: selectInfo.endStr,
          allDay: selectInfo.allDay
        })
      }
    },

    handleEventClick(clickInfo) {
      if (confirm(`Are you sure you want to delete the event '${clickInfo.event.title}'`)) {
        clickInfo.event.remove()
      }
    },
     
    handleEvents(events) {
      this.currentEvents = events
    },
    
  }
}
</script>

<template>

  <div class='demo-app'>
    <div class='demo-app-sidebar' style="display:none">
      <div class='demo-app-sidebar-section'>
        <h2>Instructions</h2>
        <ul>
          <li>Select dates and you will be prompted to create a new event</li>
          <li>Drag, drop, and resize events</li>
          <li>Click an event to delete it</li>
        </ul>
      </div>
      <div class='demo-app-sidebar-section'>
        <label>
          <input
            type='checkbox'
            :checked='calendarOptions.weekends'
            @change='handleWeekendsToggle'
          />
          toggle weekends
        </label>
      </div>
      <div class='demo-app-sidebar-section'>
        <h2>All Events ({{ currentEvents.length }})</h2>
        <ul>
          <li v-for='event in currentEvents' :key='event.id'>
            <b>{{ event.startStr }}</b>
            <i>{{ event.title }}</i>
          </li>
        </ul>
      </div>
    </div>

    <div class='demo-app-main'>
      <div class="wrap-right">
    <div id='external-events'>
      <h4>Drivers list</h4>

      <div id='external-events-list'>
        <div class='fc-event fc-h-event fc-daygrid-event fc-daygrid-block-event'>
          <div class='fc-event-main' id="dr1">Driver 1</div>
        </div>
        <div class='fc-event fc-h-event fc-daygrid-event fc-daygrid-block-event'>
          <div class='fc-event-main' id="dr2">Driver 2</div>
        </div>
        <div class='fc-event fc-h-event fc-daygrid-event fc-daygrid-block-event'>
          <div class='fc-event-main' id="dr3">Driver 3</div>
        </div>
        <div class='fc-event fc-h-event fc-daygrid-event fc-daygrid-block-event'>
          <div class='fc-event-main' id="dr4">Driver 4</div>
        </div>
        <div class='fc-event fc-h-event fc-daygrid-event fc-daygrid-block-event'>
          <div class='fc-event-main' id="dr5">Driver 5</div>
        </div>
      </div>

      <p>
        <input type='checkbox' id='drop-remove' />
        <label for='drop-remove'>remove after drop</label>
      </p>
    </div>


    <div id='external-events-2'>
      <h4>Tractors list</h4>

      <div id='external-events-list-2'>
        <div class='fc-event fc-h-event fc-daygrid-event fc-daygrid-block-event'>
          <div class='fc-event-main' id="tr1">Tractors event 1</div>
        </div>
        <div class='fc-event fc-h-event fc-daygrid-event fc-daygrid-block-event'>
          <div class='fc-event-main' id="tr2">Tractors event 2</div>
        </div>
        <div class='fc-event fc-h-event fc-daygrid-event fc-daygrid-block-event'>
          <div class='fc-event-main' id="tr3">Tractors event 3</div>
        </div>
        <div class='fc-event fc-h-event fc-daygrid-event fc-daygrid-block-event'>
          <div class='fc-event-main' id="tr4">Tractors event 4</div>
        </div>
        <div class='fc-event fc-h-event fc-daygrid-event fc-daygrid-block-event'>
          <div class='fc-event-main' id="tr5">Tractors event 5</div>
        </div>
      </div>

      <p>
        <input type='checkbox' id='drop-remove' />
        <label for='drop-remove'>remove after drop</label>
      </p>
    </div>
  </div>
      <FullCalendar
        class='demo-app-calendar'
        :options='calendarOptions'
      >
        <template v-slot:eventContent='arg'>
          <b>{{ arg.timeText }}</b>
          <i>{{ arg.event.title }}</i>
        </template>
      </FullCalendar>
      
    </div>
  </div>
</template>

<style lang='css'>
#external-events {
   position: relative;
    
    right: 40px;
    top: 20px;
    width: 200px;
    padding: 0 10px;
    border: 1px solid #ccc;
    background: #eee;
    text-align: left;
  }
  #external-events-2 {
   position: relative;
    
    right: 40px;
    top: 80px;
    width: 200px;
    padding: 0 10px;
    border: 1px solid #ccc;
    background: #eee;
    text-align: left;
  }
  #external-events h4 {
    font-size: 16px;
    margin-top: 0;
    padding-top: 1em;
  }

  #external-events .fc-event {
    margin: 3px 0;
    cursor: move;
  }

  #external-events p {
    margin: 1.5em 0;
    font-size: 11px;
    color: #666;
  }

  #external-events p input {
    margin: 0;
    vertical-align: middle;
  }
  #external-events-2 h4 {
    font-size: 16px;
    margin-top: 0;
    padding-top: 1em;
  }

  #external-events-2 .fc-event {
    margin: 3px 0;
    cursor: move;
  }

  #external-events-2 p {
    margin: 1.5em 0;
    font-size: 11px;
    color: #666;
  }

  #external-events-2 p input {
    margin: 0;
    vertical-align: middle;
  }

  
.wrap-right{
  float: right;
}
h2 {
  margin: 0;
  font-size: 16px;
}

ul {
  margin: 0;
  padding: 0 0 0 1.5em;
}

li {
  margin: 1.5em 0;
  padding: 0;
}

b { /* used for event dates/times */
  margin-right: 3px;
}

.demo-app {
  display: flex;
  min-height: 100%;
  font-family: Arial, Helvetica Neue, Helvetica, sans-serif;
  font-size: 14px;
}

.demo-app-sidebar {
  width: 300px;
  line-height: 1.5;
  background: #eaf9ff;
  border-right: 1px solid #d3e2e8;
}

.demo-app-sidebar-section {
  padding: 2em;
}

.demo-app-main {
  flex-grow: 1;
  padding: 3em;
}

.fc { /* the calendar root */
  max-width: 1100px;
  margin: 0 auto;
}

</style>
