<template>
  <div class="flex flex-col justify-center align-center">
    <div v-if="finished" class="flex flex-col items-center">
      <p class="text-5xl">Daily Finished!</p>
      <p class="text-3xl">Total effective time</p>
      <p class="text-6xl text-gray-100">{{ dailyTotalTime | time }}</p>
    </div>
    <Counter v-else seconds="120" @next="onNext" @start="started = true" />

    <div v-if="!finished" class="mt-16 flex justify-center">
      <button @click="shufflePeople" class="button" :disabled="started">
        <IconShuffle />
      </button>
    </div>

    <SectionTitle>Available people</SectionTitle>
    <PeopleList
      :items="availablePeople"
      :selected="currentPersonIndex"
      :action-enabled="!started && !finished"
    />

    <SectionTitle v-if="awayPeople.length > 0" :action-enabled="!started">
      Away people
    </SectionTitle>
    <PeopleList :items="awayPeople" :action-enabled="!started && !finished" />
  </div>
</template>

<script>
import md5 from 'md5';
import { shuffle } from 'lodash';
import { db } from '../db';
import Counter from '@/components/Counter.vue';
import PeopleList from '@/components/PeopleList.vue';
import SectionTitle from '@/components/SectionTitle.vue';
import IconShuffle from '@/components/icons/IconShuffle.vue';

export default {
  name: 'home',

  components: {
    Counter,
    PeopleList,
    SectionTitle,
    IconShuffle
  },

  async created() {
    const querySnapshot = await db.collection('people').get();
    this.people = querySnapshot.docs.map(doc => {
      const person = doc.data();
      let hash = '';
      if (!person.avatar && person.email) {
        hash = md5(person.email.trim().toLowerCase(), {
          encoding: 'binary'
        });
      }
      const avatar = `https://gravatar.com/avatar/${hash}`;
      return {
        ...person,
        avatar,
        available: true
      };
    });
  },

  data() {
    return {
      started: false,
      finished: false,
      currentPersonIndex: 0,
      people: []
    };
  },

  computed: {
    availablePeople() {
      return this.people.filter(person => person.available);
    },

    awayPeople() {
      return this.people.filter(person => !person.available);
    },

    dailyTotalTime() {
      return this.availablePeople.reduce(
        (dailyTotalTime, person) => dailyTotalTime + person.totalTime,
        0
      );
    }
  },

  methods: {
    shufflePeople() {
      this.people = shuffle(this.people);
      this.currentPersonIndex = 0;
    },

    onNext(personTotalTime) {
      this.availablePeople[this.currentPersonIndex].totalTime = personTotalTime;

      if (this.currentPersonIndex < this.availablePeople.length - 1) {
        this.currentPersonIndex++;
      } else {
        this.currentPersonIndex = -1;
        this.finished = true;
      }
    }
  }
};
</script>
