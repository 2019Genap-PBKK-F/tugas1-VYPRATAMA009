<template lang='html'>
  <div class='wrapper-jexcel'>
    <br>
    <input
      type='button'
      value='Add new row'
      @click='jExcelObj.insertRow()'
    />
    <br />
    <br />
    <div id='spreadsheet' ref='spreadsheet'></div>
  </div>
</template>

<script>
import jexcelStyle from 'jexcel/dist/jexcel.css' // eslint-disable-line no-unused-vars
import jexcel from 'jexcel' // eslint-disable-line no-unused-vars
import axios from 'axios'
export default {
  name: 'jexcel',
  data() {
    return {
      bio: [
        {
          'id': 0,
          'nrp': 51117123,
          'name': 'adit',
          'angkatan': 2015,
          'dob': '1991-05-22',
          'img': '',
          'aktif': true
        }
      ],
      test: {
        'id': 1,
        'nrp': 51117124,
        'name': 'dodit',
        'angkatan': 2015,
        'dob': '',
        'img': '',
        'aktif': true
      }
    }
  },
  computed: {
    jExcelOptions() {
      return {
        data: this.bio,
        columns: [
          { type: 'numeric', title: 'NRP', width: '120px', name: 'nrp' },
          { type: 'text', title: 'Nama', width: '120px', name: 'name' },
          { type: 'numeric', title: 'Angkatan', width: '120px', name: 'angkatan' },
          { type: 'calendar', title: 'Tanggal lahir', width: '250px', name: 'dob' },
          { type: 'image', title: 'Photo', width: '120px', name: 'img' },
          { type: 'checkbox', title: 'Aktif', width: '80px', name: 'aktif' }
        ]
      }
    }
  },
  methods: {
    load() {
      axios.get('http://localhost:3000/mahasiswa/1').then(response => {
        var valuesOnly = Object.values(response.data)
        valuesOnly.shift()
        this.res.push(valuesOnly)
      }).catch((err) => {
        console.log(err)
      })
    }
  },
  mounted() {
    axios.get('http://localhost:3000/mahasiswa/0').then(response => {
      var valuesOnly = Object.values(response.data)
      valuesOnly.shift()
      this.bio.push(response.data)
      this.bio.push(this.test)
      this.test = response.data
      console.log(this.bio)
    }).catch((err) => {
      console.log(err)
    })
    const jExcelObj = jexcel(this.$refs['spreadsheet'], this.jExcelOptions)
    Object.assign(this, { jExcelObj }) // tucks all methods under jExcelObj object in component instance
  }
}
</script>

<style lang='css' scoped>
.introduction {
  font-size: 14px;
  padding: 0.5em;
  margin-bottom: 0.3em;
  color: gold;
  background: #111;
}
</style>