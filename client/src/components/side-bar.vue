<template lang="pug">
el-menu(
  :default-active="$route.params.keyspace"
  class="el-menu-vertical-demo"
  v-loading="loading"
  :collapse-transition="false"
  :collapse="isCollapse")

  //- NOTE: keyspace
  el-menu-item(
    v-for="v,i in keyspace"
    :key="v.keyspace_name"
    @click="goToKeyspace(v.keyspace_name)"
    :index="v.keyspace_name")
      i(class="el-icon-tickets")
      span(lot="title") {{v.keyspace_name}}

  //- NOTE: query
  el-menu-item(
    @click="openQuery"
    index="-1")
      i(class="el-icon-edit")
      span(lot="title") Query

  //- NOTE: query
  el-menu-item(
    @click="goHostInfo"
    index="-2")
      i(class="el-icon-printer")
      span(lot="title") HostInfo

  //- NOTE: folding menu
  el-menu-item(
    @click="toggleMenu"
    index="-3")
      i(v-bind:class="[isCollapse ? 'el-icon-caret-right' : 'el-icon-caret-left']")
      span(lot="title") Folding menu
</template>
<style>
</style>

<script>
import api from '@/api'

const service = api.make('root')

export default {
  name: 'side-bar',

  data() {
    return {
      isCollapse: false,
      keyspace: [],
      loading: false,
    }
  },

  created() {
    this.fetch()
  },

  methods: {
    async fetch() {
      this.loading = true

      try {
        const res = await service.request('keyspace')

        this.keyspace = res.get()
      } catch (error) {
        this.$message({
          type: 'error',
          showClose: true,
          message: error
        });
      }

      this.loading = false
    },

    async goToKeyspace(keyspace) {
      this.$router.push({
        name: 'table-list',
        params: {
          keyspace
        }
      })
    },

    async goHostInfo() {
      this.$router.push({
        name: 'hostinfo'
      })
    },

    openQuery() {
      this.$prompt('Enter Query', 'CQL Query', {
        confirmButtonText: 'Execute',
        cancelButtonText: 'Cancel',
        inputType: 'textarea',
      }).then(async ({ value }) => {
        this.$router.push({
          name: 'query',
          params: {
            query: `${value}`
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: 'Cancel Query'
        })
      })
    },

    toggleMenu() {
      this.isCollapse = !this.isCollapse
    },
  }
};
</script>
