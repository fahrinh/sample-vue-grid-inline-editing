<template>
  <div id="app" class="container-fluid">
    <div class="row">
      <div class="col-md-2"></div>
      <div class="col-md-10">

        <div class="panel panel-default">
          <div class="panel-heading">Sample Vue Grid Inline Editing</div>
          <div class="panel-body">
            <form class="form-horizontal">
              <div class="form-group">
                <label class="col-sm-2 control-label">Kode</label>
                <div class="col-sm-6">
                  <input class="form-control" placeholder="Kode" v-model="cmd.kode" />
                </div>
              </div>
              <div class="form-group">
                <label class="col-sm-2 control-label">Alamat</label>
                <div class="col-sm-6">
                  <input class="form-control" placeholder="Alamat" v-model="cmd.alamat" />
                </div>
              </div>
            </form>

            <table class="table table-bordered table-condensed">
              <thead>
                <tr>
                  <th>No</th>
                  <th class="cursor" @click="sortField('id')">ID<i class="glyphicon" :class="[showSortIcon('id')]" />
                  </th>
                  <th class="cursor" @click="sortField('nama')">Nama<i class="glyphicon" :class="[showSortIcon('nama')]" />
                  </th>
                  <th class="cursor" @click="sortField('wilayah')">Wilayah<i class="glyphicon" :class="[showSortIcon('wilayah')]" />
                  </th>
                  <th class="cursor" @click="sortField('tanggal')">Tanggal<i class="glyphicon" :class="[showSortIcon('tanggal')]" />
                  </th>
                  <th class="cursor" @click="sortField('waktu')">Waktu<i class="glyphicon" :class="[showSortIcon('waktu')]" />
                  </th>
                  <th class="cursor" @click="sortField('keterangan')">Keterangan<i class="glyphicon" :class="[showSortIcon('keterangan')]" />
                  </th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item, index) in listPaginationItem">

                  <td>{{ showNo(index) }}</td>
                  <td>{{ item.id }}</td>
                  <td class="form-group" :class="{'danger': showError(item, 'nama') }">
                    <input class="form-control" :value="item.nama" @blur="onChangeName(index, item, $event)" />
                    <div class="text-danger"> {{ showError(item, 'nama') }} </div>
                  </td>
                  <td class="form-group" :class="{'danger': showError(item, 'wilayah') }">
                    <multiselect :value="item.wilayah" track="id" label="namaWilayah" :show-labels="false" :options="listWilayah" @input="onChangeWilayah(index, item, $event)">
                    </multiselect>
                    <div class="text-danger"> {{ showError(item, 'wilayah') }} </div>
                  </td>
                  <td class="form-group" :class="{'danger': showError(item, 'tanggal') }">
                    <datepicker :value="item.tanggal" @selected="onChangeTanggal(index, item, $event)"></datepicker>
                    <div>
                      <!--<vue-flatpickr :options="tanggalOptions"/>-->
                    </div>
                    <div class="text-danger"> {{ showError(item, 'tanggal') }} </div>
                  </td>
                  <td class="form-group" :class="{'danger': showError(item, 'waktu') }">
                    <!--<vue-timepicker
            format="HH:mm"
            :time-value.sync="item.waktu"
            @change="onChangeWaktu(index, item, $event)"
            ></vue-timepicker>-->
                    <jquery-timepicker class="form-control" :value="item.waktu" @input="onChangeWaktu2(index, item, $event)" />
                    <div class="text-danger"> {{ showError(item, 'waktu') }} </div>
                  </td>
                  <td>
                    {{ item.keterangan }}
                  </td>
                </tr>
              </tbody>
            </table>

            <nav>
              <ul class="pager">
                <li>
                  <a href="#" @click="movePage(-1)"> <i class="glyphicon glyphicon-chevron-left" /> Previous</a>
                </li>
                {{ startRow }} - {{ endRow }} dari {{ cmd.listItem.length}} data
                <li><a href="#" @click="movePage(1)">Next <i class="glyphicon glyphicon-chevron-right"/></a></li>
              </ul>
            </nav>

            <div class="row">
              <div class="col-md-4"></div>
              <div class="col-md-5 col-md-offset-4">
                <input class="btn btn-success" type="submit" value="Simpan">
                <input @click="testError()" class="btn btn-danger" type="submit" value="Test Random Error" />
                <input @click="clearError()" class="btn btn-info" type="submit" value="Clear Error" />
              </div>
            </div>

          </div>
        </div>
      </div>

    </div>
  </div>
</template>
<script>
  /* eslint-disable */
  import Vue from 'vue'
  import Datepicker from 'vuejs-datepicker'
  import VueFlatpickr from 'vue-flatpickr'
  import Multiselect from 'vue-multiselect'
  import VueTimepicker from 'vue2-timepicker'
  import JqueryTimepicker from './components/JqueryTimepicker'

  import uuid from 'uuid'
  import axios from 'axios'

  import 'vue-flatpickr/theme/airbnb.css'
  import 'bootstrap/dist/css/bootstrap.min.css'
  import 'bootstrap/dist/css/bootstrap-theme.min.css'

  export default {
    name: 'app',
    components: {
      Datepicker,
      Multiselect,
      VueFlatpickr,
      VueTimepicker,
      JqueryTimepicker
    },
    data() {
      return {
        cmd: {
          kode: '',
          alamat: '',
          listItem: []
        },
        mapError: {},
        mapListWilayah: {},
        listWilayah: [{
            id: 1,
            namaWilayah: 'Cirebon'
          },
          {
            id: 2,
            namaWilayah: 'Cipayung'
          },
          {
            id: 3,
            namaWilayah: 'Cikeas'
          },
          {
            id: 4,
            namaWilayah: 'Cijantung'
          },
          {
            id: 5,
            namaWilayah: 'Kalisari'
          },
          {
            id: 6,
            namaWilayah: 'Cisarua'
          },
          {
            id: 7,
            namaWilayah: 'Cimahi'
          }
        ],
        options: ['Aceh', 'Medan'],
        columns: [
          'id', 'nama', 'wilayah', 'tanggal', 'waktu', 'keterangan'
        ],
        tanggalOptions: {
          onChange: this.onChangeTanggal2
        },
        rowsPerPage: 3,
        startRow: 1,
        sort: {
          field: '',
          asc: false
        }
      }
    },
    computed: {
      listPaginationItem() {
        let thisVM = this
        let sortField = thisVM.sort.field
        let sortAsc = thisVM.sort.asc

        return this.cmd.listItem
          .sort(function (a, b) {
            let aValue = a[sortField]
            let bValue = b[sortField]

            if (sortField === 'wilayah') {
              aValue = a[sortField].namaWilayah
              bValue = b[sortField].namaWilayah
            }

            let sortValue = 0
            if (aValue < bValue) sortValue = -1
            if (aValue > bValue) sortValue = 1

            if (sortAsc) sortValue = sortValue * -1
            return sortValue
          })
          .slice(this.startRow - 1, this.startRow - 1 + this.rowsPerPage)
      },
      endRow() {
        let endRow = this.startRow - 1 + this.rowsPerPage
        if (endRow > this.cmd.listItem.length) return this.cmd.listItem.length
        return endRow
      }
    },
    created: function () {

      this.cmd.listItem = [{
          id: 1,
          nama: 'Nama 1',
          wilayah: {
            id: 7,
            namaWilayah: 'Cimahi'
          },
          tanggal: null,
          waktu: null,
          keterangan: 'testketerangan'
        },
        {
          id: 2,
          nama: 'Nama 2',
          wilayah: {
            id: 2,
            namaWilayah: 'Cipayung'
          },
          tanggal: null,
          waktu: null,
          keterangan: 'testketerangan3'
        },
        {
          id: 3,
          nama: 'Nama 3',
          wilayah: {
            id: 2,
            namaWilayah: 'Cipayung'
          },
          tanggal: null,
          waktu: null,
          keterangan: 'testketerangan'
        },
        {
          id: 4,
          nama: 'Nama 4',
          wilayah: {
            id: 2,
            namaWilayah: 'Cipayung'
          },
          tanggal: null,
          waktu: null,
          keterangan: 'testketerangan3'
        },
        {
          id: 5,
          nama: 'Nama 5',
          wilayah: {
            id: 7,
            namaWilayah: 'Cimahi'
          },
          tanggal: null,
          waktu: null,
          keterangan: 'testketerangan'
        },
        {
          id: 6,
          nama: 'Nama 6',
          wilayah: {
            id: 6,
            namaWilayah: 'Cisarua'
          },
          tanggal: null,
          waktu: null,
          keterangan: 'testketerangan3'
        },
        {
          id: 7,
          nama: 'Nama 7',
          wilayah: {
            id: 6,
            namaWilayah: 'Cisarua'
          },
          tanggal: null,
          waktu: null,
          keterangan: 'testketerangan7'
        }
      ]

      this.mapListWilayah[1] = [{
        id: 1,
        namaWilayah: 'Bandung'
      }, {
        id: 2,
        namaWilayah: 'Cimahi'
      }]
      this.mapListWilayah[2] = [{
        id: 1,
        namaWilayah: 'Sulawesi'
      }, {
        id: 2,
        namaWilayah: 'Lombok'
      }]

      this.populateUUID()

      console.log('Type mapListWilayah : ', this.mapListWilayah)
    },
    methods: {
      sortField(field) {
        this.sort.field = field
        this.sort.asc = !this.sort.asc
      },
      showSortIcon(field) {
        if (this.sort.field === field) {
          return 'glyphicon-chevron-' + (this.sort.asc ? 'up' : 'down')
        }
      },
      populateUUID() {
        let thisVM = this
        this.cmd.listItem.forEach(function (value, i) {
          //value.no = ++i
          thisVM.$set(value, 'uuid', uuid())
          console.log('%d: %o', i, value);
        })
      },
      testError() {
        let columns = ['id', 'nama', 'wilayah', 'tanggal', 'waktu', 'keterangan']
        for (let item of this.cmd.listItem) {
          let randomPickedField = columns[Math.floor(Math.random() * columns.length)]
          this.setError(item.uuid, randomPickedField, 'Ada error')

          //this.removeError(item.uuid, randomPickedField)
        }
      },
      clearError() {
        let columns = ['id', 'nama', 'wilayah', 'tanggal', 'waktu', 'keterangan']
        for (let item of this.cmd.listItem) {
          for (let field of columns) {
            this.removeError(item.uuid, field)
          }
        }
      },
      showNo(index) {
        // let currentPage = ((this.startRow - 1) / this.rowsPerPage) + 1
        // let no = (currentPage - 1) * this.rowsPerPage + (index + 1)

        return this.startRow + index
      },
      setError(uuid, field, errorMessage) {
        let error = this.mapError[uuid] || {}
        error[field] = errorMessage
        this.$set(this.mapError, uuid, Object.assign({}, error, error))
      },
      removeError(uuid, field) {
        let error = this.mapError[uuid] || {}
        error[field] = undefined
      },
      showError(item, field) {
        let uuid = item.uuid
        let error = this.mapError[uuid]
        console.log('error : ', uuid, field, error)
        if (error === undefined) return null
        return error[field]
      },
      movePage(amountPage) {
        var newStartRow = this.startRow + (amountPage * this.rowsPerPage);
        if (newStartRow >= 1 && newStartRow <= this.cmd.listItem.length) {
          this.startRow = newStartRow;
        }
      },
      onChangeName(index, item, event) {
        let oldName = item.nama
        let oldKeterangan = item.keterangan

        console.log('[Name] ON CHANGED BEFORE index: ', index, ' item: ', oldName)

        item.nama = event.target.value

        item.keterangan = oldKeterangan + ' >' + item.nama

        console.log('[Name] ON CHANGED AFTER index: ', index, ' item: ', item)

        if (item.nama === 'error') {
          this.setError(item.uuid, 'waktu', 'tes method baru wilayah')
        }
      },
      onChangeTanggal(index, item, selectedDate) {
        console.log('item [', selectedDate.getDate(), ']')
        let oldTanggal = item.tanggal

        console.log('[Tanggal] ON CHANGED BEFORE index: ', index, ' item: ', oldTanggal)

        item.tanggal = selectedDate

        console.log('[Tanggal] ON CHANGED AFTER index: ', index, ' item: ', item)
      },
      onChangeTanggal2(selectedDates, dateStr, instance) {
        console.log('selectedDates: ', selectedDates, ' dateStr: ', dateStr, ' instance: ', instance)
      },
      onChangeWilayah(index, item, selectedWilayah) {
        let oldWilayah = item.wilayah

        // console.log('selectedWilayah : ', selectedWilayah)
        console.log('[Wilayah] ON CHANGED BEFORE index: ', index, ' item: ', oldWilayah)

        item.wilayah = selectedWilayah

        console.log('[Wilayah] ON CHANGED AFTER index: ', index, ' item: ', item)
      },
      onChangeWaktu(index, item, selectedWaktu) {
        let oldWaktu = item.waktu

        console.log('selectedWaktu : ', selectedWaktu)
        // console.log('selectedWilayah : ', selectedWilayah)
        console.log('[Waktu] ON CHANGED BEFORE index: ', index, ' item: ', oldWaktu)

        item.waktu = selectedWaktu.data

        console.log('[Waktu] ON CHANGED AFTER index: ', index, ' item: ', item)
      },
      onChangeWaktu2(index, item, selectedWaktu) {
        console.log('*** onChangeWaktu2')
        let oldWaktu = item.waktu

        console.log('selectedWaktu : ', selectedWaktu)
        // console.log('selectedWilayah : ', selectedWilayah)
        console.log('[Waktu] ON CHANGED BEFORE index: ', index, ' item: ', oldWaktu)

        item.waktu = selectedWaktu

        console.log('[Waktu] ON CHANGED AFTER index: ', index, ' item: ', item)
      }
    }
  }

</script>
<style>
  .cursor {
    cursor: pointer;
  }

</style>
