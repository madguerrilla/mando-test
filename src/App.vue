<template>
<div class="l-default">
  <div class="o-user">
    <div class="c-user__avatar"></div>
    <div class="c-user__details">
      <div class="c-user__details-info">
        <h1 class="c-user__details-info-name">{{userData.name}}</h1>
        <h2 class="c-user__details-info-username">(Your Username: {{userData.username}})</h2>
      </div>
      <p class="c-user__details-bio">
        {{userData.bio}}
      </p>

      <div class="c-user__details-progress">
        <div class="c-user__details-progress-bar">
          <div class="c-user__details-progress-bar-value" :style="{maxWidth: completion() + '%'}" :data-value="completion ()"></div>
        </div>
        <h2 class="c-user__details-progress-completion">Completed {{completion () }}&#37; of modules</h2>
        <ul class="c-user__details-progress-list">
          <li class="c-user__details-progress-list-item">
            {{modulesCalc ('completed') }} Modules Completed
          </li>
          <li class="c-user__details-progress-list-item">
            {{modulesCalc ('progress') }} Modules Progress
          </li>
          <li class="c-user__details-progress-list-item">
            {{modulesCalc ('unstarted') }} Modules Remaining
          </li>
        </ul>
      </div>
    </div>
  </div>
  <div class="o-filters">
    <button class="c-filters__button" @click="filter()">Show All</button>
    <button class="c-filters__button" @click="filter('html')">HTML</button>
    <button class="c-filters__button" @click="filter('javascript')">JavaScript</button>
    <button class="c-filters__button" @click="filter('CSS')">CSS</button>
  </div>
  <div class="o-courses">
    <div :id="course.courseId" v-for="course in courses" class="c-course">
      <div :class="'c-course__icon c-course__icon--' + course.category.toLowerCase().trim()"></div>
      <div :class="'c-course__status c-course__status--' + course.status">{{status (course.status) }}</div>
      <div class="c-course__description">
        <h4>{{course.title}}</h4>
        <p>
          {{course.description}}
        </p>
      </div>
      <div class="c-course__details">
        <div class="c-course__details-more" @click="expand(course.courseId)">Course Details</div>
        <ul :id="'c-course__details-list' + course.courseId" class="c-course__details-list">
          <li class="c-course__details-list-item" v-for="detail in course.details">
            {{detail}}
          </li>
        </ul>
      </div>
    </div>
  </div>
  <div class="o-form">
    <form v-on:submit.prevent>
      <label for="name">Name</label>
      <input v-model="name" id="name" class="c-form__input" type="text" required />
      <span class="c-form__error" :class="{'c-form__error--active': !name}">You must complete this field</span>
      <label for="bio">Bio</label>
      <textarea required v-model="bio" id="bio" class="c-form__text"></textarea>
      <span class="c-form__error" :class="{'c-form__error--active': !bio}">You must complete this field</span>
      <button class="c-form__button" @click="submit()">Submit</button>
    </form>
  </div>
</div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'app',
  data() {
    return {
      userData: null,
      courses: null,
      name: null,
      bio: null
    }
  },
  mounted() {
    this.getUserData()
  },
  methods: {
    getUserData: function() {
      axios
        .get('/static/data.json')
        .then((response) => {
          console.log(response)
          this.userData = response.data;
          this.courses = response.data.courses;
        })
    },
    modulesCalc: function(type) {
      let count = 0
      this.courses.filter((val) => {
        if (val.status === type) count++
      })
      return count
    },
    completion: function() {
      const courseCount = this.userData.courses.length
      const completed = this.modulesCalc('completed')
      return (completed / courseCount * 100)
    },
    filter: function(cat) {
      this.courses = this.userData.courses
      if (cat == null) {
        return
      } else {
        this.courses = this.courses.filter((val) => {
          if (val.category === cat) return val
        })
      }
    },
    status: function(status) {
      switch (status) {
        case 'completed':
          return 'Course completed'
          break;
        case 'progress':
          return 'In progress'
          break;
        case 'unstarted':
          return 'Not started'
          break;
      }
    },
    expand: function(i) {
      event.target.classList.toggle('c-course__details-more--active');
      const list = document.getElementById('c-course__details-list' + i);
      list.classList.toggle('c-course__details-list--show');
    },
    submit: function() {
      if (this.name && this.bio) {
        this.userData.name = this.name;
        this.userData.username = this.name.split('').reverse().join('') + Math.floor(Math.random() * 90000) + 10000;
        this.userData.bio = this.bio;
      }
    }
  }
}
</script>

<style lang="scss" scoped>
$green: #aadb1e;
$darkGrey: #565656;
$midGrey: #b7b7b7;
$grey: #ccc;
$lightGrey: #f4f4f4;
$white: #fff;
*,
*:after,
*:before {
    box-sizing: border-box;
}

html {
    font-size: 62.5%;
}

.l {
    &-default {
        font-family: Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        color: $darkGrey;
        max-width: 1200px;
        width: 100%;
        margin: 24px auto;
        font-size: 1rem;
    }
}

.o {
    &-user {
        border-radius: 4px;
        border: 1px solid $grey;
        width: 100%;
        padding: 16px;
        min-height: 196px;
        display: flex;
        flex-flow: column;
        @media (min-width: 768px) {
            flex-flow: row;
        }
    }
    &-courses {
        margin: 16px 0;
        width: 100%;
        @media (min-width: 768px) {
            grid-template-columns: repeat(3, 1fr);
            display: grid;
            grid-template-rows: auto;

            .c-course:first-child {
                margin-left: 0;
            }
            .c-course:last-child {
                margin-right: 0;
            }
        }
        @media (min-width: 992px) {
            grid-template-columns: repeat(4, 1fr);
        }
    }
    &-filters {
        margin: 24px 0;
        display: flex;
        align-items: center;
        flex-flow: column;
        @media (min-width: 768px) {
            flex-flow: row;
        }
        .c-filters__button:first-child {
            margin-left: 0;
        }
    }
    &-form {
        width: 100%;
        border-radius: 4px;
        border: 1px solid $grey;
        padding: 32px;
        form {
            display: flex;
            flex-flow: column;
        }
    }
}

.c {
    &-user {
        &__avatar,
        &__details {
            width: 100%;
        }
        &__avatar {
            padding: 32px;
            background: url("./assets/svgs/avatar.svg") no-repeat top center;
            background-size: contain;
            @media (min-width: 768px) {
                max-width: 10%;
            }
        }
        &__details {
            @media (min-width: 768px) {
                max-width: 60%;
            }
            padding: 32px;
            &-info {
                display: flex;
                align-items: baseline;

                &-name,
                &-username {
                    display: inline;
                }
                &-name {
                    font-size: 2rem;
                }
                &-username {
                    font-size: 1rem;
                    padding-left: 8px;
                }
            }
            &-progress {

                &-bar {
                    border-radius: 5rem;
                    border: 1px solid $grey;
                    padding: 8px;
                    margin: 32px 0;
                    &-value {
                        width: 100%;
                        height: 16px;
                        background-color: $green;
                        border-radius: 5rem;
                        overflow: hidden;
                        background-color: $green!important;
                    }
                }

                &-list {
                    list-style: none;
                    padding: 0;
                    margin: 0;
                    &-item {
                        font-weight: bold;
                        margin-bottom: 8px;
                    }
                }
            }
        }
    }
    &-course {
        border-radius: 4px;
        border: 1px solid $grey;
        margin-left: 16px;
        height: fit-content;
        margin-bottom: 24px;
        &__icon {
            background-repeat: no-repeat;
            background-size: contain;
            background-position: center;
            width: 100%;
            height: 96px;
            padding: 16px;
            margin: 32px auto;
            &--html {
                background-image: url("./assets/svgs/html5.svg");
            }
            &--css {
                background-image: url("./assets/svgs/css.svg");
            }
            &--javascript {
                // background-image: url('./assets/svgs/javascript.svg');
            }
        }
        &__status {
          text-align: center;
          padding: 8px;
          font-weight: bold;
        }
        &__description {
            background-color: $lightGrey;
            padding: 16px;
            font-weight: bold;
        }
        &__details {
            &-more {
                background-color: $midGrey;
                padding: 8px 16px;
                font-weight: bold;
                display: flex;
                align-items: center;
                justify-content: space-between;
                cursor: pointer;
                &:after {
                    width: 16px;
                    height: 16px;
                    content: '';
                    background-image: url("./assets/svgs/arrows.svg");
                    background-repeat: no-repeat;
                    background-size: contain;
                    background-position: center;
                }
                &--active {
                    background-color: $darkGrey;
                    color: $white;
                    &:after {
                        background-image: url("./assets/svgs/arrows-white.svg");
                        transform: rotate(180deg);
                    }
                }
            }
            &-list {
                // padding: 0;
                // margin: 0;
                height: 0;
                overflow: hidden;
                margin-top: 0;
                margin-bottom: 0;

                &-item {
                    margin-bottom: 8px;
                    font-weight: bold;
                }
                &--show {
                    height: 100%;
                    padding-top: 16px;
                    padding-bottom: 16px;
                }
            }
        }
    }
    &-filters {
        &__button {
            padding: 16px;
            background-color: $green;
            border-radius: 4px;
            border: 0;
            font-size: 1rem;
            color: $darkGrey;
            font-weight: bold;
            min-width: 100px;
            margin-bottom: 16px;
            @media (min-width: 768px) {
                margin: 0 16px;
            }
        }
    }
    &-form {
        &__input,
        &__text {
            width: 100%;
            max-width: 400px;
            height: 48px;
            border-radius: 4px;
            border: 1px solid $grey;
            margin-bottom: 24px;
        }
        &__text {
            height: 300px;
        }
        &__button {
            padding: 16px;
            background-color: $green;
            border-radius: 4px;
            border: 0;
            font-size: 1rem;
            color: $darkGrey;
            font-weight: bold;
            width: 200px;
        }
        &__error {
            font-weight: bold;
            color: #f00;
            margin-bottom: 16px;
            display: none;
            &--active {
                display: block;
            }
        }
    }
}
</style>
