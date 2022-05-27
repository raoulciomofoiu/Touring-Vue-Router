<template>
  <h1>Events for Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />

    <div class="pagination">
      <router-link
        id="page-prev"
        :to="{ name: 'EventList', query: { page: page - 1 } }"
        rel="prev"
        v-if="page != 1">
        &#60; Previous
      </router-link>

      <div class="page-numbers">
        <router-link
          :to="{ name: 'EventList', query: { page: 1 } }"
          >
          1
        </router-link>

        <router-link
          :to="{ name: 'EventList', query: { page: 2 } }"
          >
          2
        </router-link>

        <router-link
          :to="{ name: 'EventList', query: { page: 3 } }"
          >
          3
        </router-link>
      </div>

      <router-link
        id="page-next"
        :to="{ name: 'EventList', query: { page: page + 1 } }"
        rel="next"
        v-if="hasNextPage">
        Next &#62;
      </router-link>
    </div>
  </div>
</template>

<script>
import EventCard from '@/components/EventCard.vue'
import EventService from '@/services/EventService.js'
import { watchEffect } from 'vue'

export default {
  name: 'EventList',
  props: ['page'],
  components: {
    EventCard
  },
  data() {
    return {
      events: null,
      totalEvents: 0
    }
  },
  created() {
    watchEffect(() => {
      this.events = null
      EventService.getEvents(2, this.page)
        .then(response => {
          this.events = response.data
          this.totalEvents = response.headers['x-total-count']
        })
        .catch(error => {
          console.log(error)
        })
    })
  },
  computed: {
    hasNextPage() {
      var totalPages = Math.ceil(this.totalEvents / 2)

      return this.page < totalPages
    }
  }
}
</script>

<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.pagination {
  display: flex;
  width: 290px;
}

.pagination a {
  flex: 1;
  text-decoration: none;
}

.page-numbers {
  display: flex;
  gap: 10px;
  position: fixed;
  margin-left: 130px;
}

#page-prev {
  text-align: left;
}

#page-next {
  text-align: right;
}

</style>
