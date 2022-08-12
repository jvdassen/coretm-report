<template>
  <div id="front">
    <vue-good-table
      :columns="columns"
      :rows="threats"/>

  </div>
</template>

<script>
import 'vue-good-table/dist/vue-good-table.css'
import { VueGoodTable } from 'vue-good-table';

// add to component
export default {
  name: 'App',
  data: () => ({
    columns: [
      {
        label: 'ID',
        field: 'id'
      },
      {
        label: 'Asset',
        field: 'label'
      }
/*      {
        label: 'Description',
        field: 'description'
      }*/
    ],
    threats: [
    ]
  }),
  components: {
    VueGoodTable,
  },
  mounted () {
    var diagram = viewer.getBundleById('com.vonderassen.coretm')
    var model = diagram.getDizmos()[0]
    var data = model.publicStorage.getProperty('diagramdecoded')
    console.log(this)
    processModel.bind(this)('', data)
    model.publicStorage.subscribeToProperty('diagramdecoded', processModel.bind(this))

    function processModel (_, changes) {
      var { data } = changes

      var parser = new DOMParser()
      var xmlDoc = parser.parseFromString(data, 'text/xml')
      window.xmlDoc = xmlDoc
      var threatRecords = [...xmlDoc.querySelectorAll('[coretm-type=threat]')]
      var assetObjects = [...xmlDoc.querySelectorAll('mxCell')].filter(e => e?.attributes['style']?.textContent.includes('E3C800'))
      console.log(assetObjects)
      var assetCells = assetObjects.map(a => a.parentNode)
      var threats = assetCells.map((el) => {
        return {
          id: el.id,
          label: el.getAttribute('label').replaceAll('<br>', ' ').replaceAll('<div>', ' ').replaceAll('</div>', ' '),
          description: el.getAttribute('description') || '...'
        }
      })
      this.threats = threats
    }
  }
};
</script>

<style lang="scss">
table.vgt-table {
  background-color: #2d2c2c !important;
  border: #535151 !important;
}

.vgt-table.bordered td, .vgt-table.bordered th {
  border: none !important;
}

.vgt-table thead th {
  color: #edf3ff !important;
  background: #3d3d3d !important;
}

table.vgt-table td {
  color: #c8cedb !important;
}
</style>

