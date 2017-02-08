<template lang="html">
  <div class="container">
    <h2>telemetR tables</h2>
    <p>A list of table data retrieved form the <code>telemetr-api</code>. Click the buttons below to view data from the different tables in the database</p>

    <div class="btn-group btn-group-justified" role="group">



    </div>

    <div class="btn-group btn-group-justified" role="group" aria-label="...">
      <div class="btn-group" role="group">
        <button type="button" class="btn btn-primary" :class="devices.show ? 'btn-success' : 'btn-primary'" @click="getDevices">Devices</button>
      </div>
      <div class="btn-group" role="group">
        <button type="button" class="btn btn-primary" :class="captures.show ? 'btn-success' : 'btn-primary'" @click="getCaptures">Captures</button>
      </div>
      <div class="btn-group" role="group">
        <button type="button" class="btn" :class="deployments.show ? 'btn-success' : 'btn-primary'" @click="getDeployments">Deployments</button>
      </div>
      <div class="btn-group" role="group">
        <button type="button" class="btn" :class="relocs.show ? 'btn-success' : 'btn-primary'" @click="getRelocs">GPS Count</button>
      </div>
    </div>

    <div>

      <!-- deployments table -->
      <table class="table table-striped" v-show="deployments.show">
        <thead>
          <tr>
            <th>Perm ID</th>
            <th>Serial</th>
            <th>Inservice</th>
            <th>outservice</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="row in deployments.data.data">
            <td>{{ row.perm_id }}</td>
            <td>{{ row.serial_num }}</td>
            <td>{{ row.inservice }}</td>
            <td>{{ row.outservice }}</td>
          </tr>
        </tbody>
      </table>

      <!-- capture table -->
      <table class="table table-striped" v-show="captures.show">
        <thead>
          <tr>
            <th>Perm ID</th>
            <th>Species</th>
            <th>Age</th>
            <th>Sex</th>
            <th>Serial</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="row in captures.data.data">
            <td>{{ row.perm_id }}</td>
            <td>{{ row.species }}</td>
            <td>{{ row.age }}</td>
            <td>{{ row.sex }}</td>
            <td>{{ row.serial_num }}</td>
          </tr>
        </tbody>
      </table>

      <!-- devices table -->
      <table class="table table-striped" v-show="devices.show">
        <thead>
          <tr>
            <th>Serial</th>
            <th>Frequency</th>
            <th>Vendor</th>
            <th>Model</th>
            <th>Type</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="row in devices.data.data">
            <td>{{ row.serial_num }}</td>
            <td>{{ row.frequency }}</td>
            <td>{{ row.vendor }}</td>
            <td>{{ row.model }}</td>
            <td>{{ row.device_type }}</td>
          </tr>
        </tbody>
      </table>

      <!-- relocs count table -->
      <table class="table table-striped" v-show="relocs.show">
        <thead>
          <tr>
            <th>Perm ID</th>
            <th>Description</th>
            <th>N Points</th>
            <th>Valid Code</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="row in relocs.data.data">
            <td>{{ row.perm_id }}</td>
            <td>{{ row.description }}</td>
            <td>{{ row.n }}</td>
            <td>{{ row.code }}</td>
          </tr>
        </tbody>
      </table>

    </div>

  </div>
</template>

<script>
import axios from 'axios'
const api = axios.create()

export default {
  data () {
    return {
      devices: {
        show: false,
        data: []
      },
      captures: {
        show: false,
        data: []
      },
      deployments: {
        show: false,
        data: []
      },
      relocs: {
        show: false,
        data: []
      }
    }
  },

  methods: {
    getDeployments () {
      this.deployments.show = true
      this.devices.show = false
      this.captures.show = false
      this.relocs.show = false

      api.get('http://localhost:8081/deployments')
      .then(data => {
        console.log(data)
        this.deployments.data = data.data
      })
      .catch(err => console.log(err))
    },

    getCaptures () {
      this.deployments.show = false
      this.devices.show = false
      this.captures.show = true
      this.relocs.show = false

      api.get('http://localhost:8081/captures')
      .then(data => {
        this.captures.data = data.data
      })
      .catch(err => console.log(err))
    },

    getDevices () {
      this.deployments.show = false
      this.devices.show = true
      this.captures.show = false
      this.relocs.show = false

      api.get('http://localhost:8081/devices')
      .then(data => {
        this.devices.data = data.data
      })
      .catch(err => console.log(err))
    },

    getRelocs () {
      this.deployments.show = false
      this.devices.show = false
      this.captures.show = false
      this.relocs.show = true

      api.get('http://localhost:8081/validity/relocations')
      .then(data => {
        this.relocs.data = data.data
      })
      .catch(err => console.log(err))
    }
  },

  created: function () {
    this.getDeployments()
  }
}
</script>

<style lang="css" scoped>
.btn-group {
  padding-bottom: 15px;
  padding-top: 15px;
  width: 100%
}

button {
  border-color: white;
}

th {
  text-align: center;
}
</style>
