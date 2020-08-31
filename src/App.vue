<template>
  <div id="app">
    <div class="frac-form">

      <div class="frac-formula">

        <template v-for="(frac, index) in fractions">          
          <FractInput class="frac-formula-el" 
            :key="frac.id" 
            :id="frac.id" 
            :data="frac.value" 
            :removable="fractions.length > min"
            @remove="remove"
            @update="update"
          />

          <div class="frac-formula-el" :key="`sign-${frac.id}`">
            {{ (++index) == fractions.length ? ' = ' : ' + ' }}
          </div>
        </template>

        <div class="frac-formula-el result" v-text="result"></div> 

      </div>

      <div class="frac-action" :class="{ hidden: fractions.length >= max }">
        <button @click.prevent="add">Add new element</button>
      </div> 

    </div>    
  </div>
</template>

<script>

import FractInput from './components/FractInput.vue';

let fractionsExample = [
  {
    'id': 'id-1',
    'value': '1/2'
  },
  {
    'id': 'id-2',
    'value': '1/4'
  },
];

export default {
  name: 'App',

  components: {
    FractInput
  },

  created() {
    this.fractions = fractionsExample;
  },

  data() {
    return {
      fractions: [],
      min: 2,
      max: 5,
    }
  },

  computed: {
    result() {
      let regex = new RegExp("^([1-9]{1}[0-9]?)\\/([1-9]{1}[0-9]?)$");
      let res = 0;
      
      for (let frac of this.fractions) {
        if ( regex.test(frac.value) ) {
          let match = frac.value.match(regex);
          res += match[1]/match[2];
        }
      }

      return res ? res.toFixed(2) : ''; 
    },    
  },

  methods: {
    add() {
      if (this.fractions.length < this.max) {
        this.fractions.push({'id': 'id-' + new Date().getTime()});
      }
    },

    remove(id) {
      if (this.fractions.length > this.min) {
        let filtered = this.fractions.filter((frac) => frac.id !== id)
        this.fractions = filtered;
      }
    },

    update(id, data) {
      let index = this.fractions.map(filtered => filtered.id).indexOf(id);
      let frac = {};
      frac.id = id;
      frac.value = data;
      this.fractions.splice(index, 1, frac);
    },
  },

}
</script>

<style lang="scss">

  @import "src/assets/styles/_variables.scss";

  * {
    padding: 0;
    margin: 0;
    font-family: inherit;
  }

  html, body {
    height: 100%;
  }

  body {
    background-color: $brand-color;
    font-family: 'Roboto', sans-serif;
    color: $font-color;
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
  }

  #app {
    display: flex;
    flex-direction: row;
    flex-wrap: nowrap;
    justify-content: center;
    align-content: stretch;
    align-items: center;
    width: 100%;
    height: 100%;
    text-align: center;
  }

  .frac {

    &-formula {
      display: flex;
      flex-direction: row;
      flex-wrap: wrap;
      justify-content: center;
      align-content: stretch;
      align-items: center;

      &-el {
        font-size: 46px;
        padding: 8px;

        &.result {
          font-size: 32px;
        }         
      }
    }

    &-action {

      &.hidden {
        visibility: hidden;
      }

      button {
        padding: 8px 16px;
        margin: 16px auto;
        height: 48px;
        line-height: 32px;
        font-size: 22px;
        color: $font-color;
        background: $brand-color;
        border: 2px solid $font-color;
        border-radius: $border-radius;
        cursor: pointer;
        transition: color .15s ease-in-out, 
                    background-color .15s ease-in-out;
        &:hover {
          color: $brand-color;
          background: $font-color;
        }
      }
    }

  }

</style>
