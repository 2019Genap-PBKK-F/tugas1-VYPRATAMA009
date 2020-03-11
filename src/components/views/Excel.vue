<template lang='html'>
  <div class='wrapper-jexcel'>
    <br>
    <input
      type='button'
      value='Tambah Data'
      @click='jExcelObj.insertRow()'
    />
    <input
     type='button'
      value='Hapus Data'
      @click='jExcelObj.deleteRow()'
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
          'ID': 0,
          'NRP': null,
          'Nama': null,
          'Angkatan': null,
          'Tgl_lahir': null,
          'active': null
        }
      ],
      template: {
        'ID': 0,
        'NRP': null,
        'Nama': null,
        'Angkatan': null,
        'Tgl_lahir': null,
        'active': null
      }
    }
  },
  computed: {
    jExcelOptions() {
      return {
        data: this.bio,
        columns: [
          { type: 'hidden', title: 'ID', width: '120px', name: 'ID' },
          { type: 'numeric', title: 'NRP', width: '120px', name: 'NRP' },
          { type: 'text', title: 'Nama', width: '120px', name: 'Nama' },
          { type: 'numeric', title: 'Angkatan', width: '120px', name: 'Angkatan' },
          { type: 'calendar', title: 'Tanggal lahir', width: '250px', name: 'Tgl_lahir' },
          { type: 'checkbox', title: 'Aktif', width: '80px', name: 'active' }
        ],
        onchange: this.changed,
        oninsertrow: this.insertedRow,
        ondeleterow: this.deleteRow
      }
    }
  },

  methods: {
    load() {
      axios
        .get('http://localhost:8027/api/mahasiswa')
        .then(response => {
          var valuesOnly = response.data.recordset
          this.bio = valuesOnly
          console.log(this.bio)
        }).catch(err => {
          console.log(err)
        })
    },
    changed(instance, cell, x, y, value) {
      x = parseInt(x)
      y = parseInt(y)
      console.log('x: ' + x + '\ny: ' + y)
      var ID = y + 1
      var data = {}
      axios.get('http://localhost:8027/api/mahasiswa/' + ID).then(response => {
        data = Object.values(response.data.recordset[0])
        data[x] = value
        console.log(data)
        axios({
          method: 'put',
          url: 'http://localhost:8027/api/mahasiswa/' + ID,
          data: {
            'ID': data[0],
            'NRP': data[1],
            'Nama': data[2],
            'Angkatan': data[3],
            'Tgl_lahir': data[4],
            'active': data[5]
          }
        }).then(response => {
          console.log(response.data)
        })
      })
    },

    insertedRow(instance) {
      axios({
        method: 'post',
        url: 'http://localhost:8027/api/mahasiswa/',
        data: {
          'NRP': null,
          'Nama': null,
          'Angkatan': null,
          'Tgl_lahir': null,
          'active': null
        }
      }).then(response => {
        console.log(response.data)
      })
    },

    deleteRow(instance, ID) {
      console.log(ID)
      axios({
        method: 'delete',
        url: 'http://localhost:8027/api/mahasiswa/' + ID,
        data: {}
      }).then(response => {
        console.log(response.data)
      })
    }
  },

  mounted() {
    this.load()
  },
  watch: {
    bio() {
      const jExcelObj = jexcel(this.$refs['spreadsheet'], this.jExcelOptions)
      Object.assign(this, { jExcelObj })
    }
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
