<template>
  <ApolloQuery
    v-slot="{ isLoading, result: { data: courses, error } }"
    :query="require('~/gql/teach/getCourses.gql')"
    :update="(data) => data.courses"
  >
    <page-title :loading="!!isLoading" :spaces="spaces" :value="title" />

    <div v-if="!error">
      <generic-filter-bar v-slot="{ filter: innerFilter, on }" v-model="filter">
        <courses-filter hide-unpublished :value="innerFilter" v-on="on" />
      </generic-filter-bar>

      <card-list
        class="mt-5"
        :card-props="{ hidePublished: true, space: 'teach' }"
        :component="component"
        :items="filteredCourses(courses, filter)"
        :items-per-page="8"
        prop-name="course"
      />
    </div>

    <div v-else>{{ $t('error.unexpected') }}</div>
  </ApolloQuery>
</template>

<script>
import CourseCard from '~/components/cards/CourseCard.vue'
import courses from '@/mixins/courses.js'
import titles from '@/mixins/titles.js'

export default {
  name: 'TeachCoursesPage',
  mixins: [courses, titles],
  data() {
    return {
      component: CourseCard,
      filter: {},
    }
  },
  head() {
    return {
      title: this.getTitle(this.title, null, 'learn'),
    }
  },
  computed: {
    spaces() {
      const items = {
        home: {
          name: 'courses',
        },
        manage: {
          name: 'manage-courses',
        },
      }

      return items
    },
    title() {
      return this.$tc('course._', 2)
    },
  },
  meta: {
    roles: ['teacher'],
  },
}
</script>
