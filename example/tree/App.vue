<template>
  <div id="app" class="container-fluid">
    <div class="col-md-3">

      <div class="panel panel-default">
        <div class="panel-heading">Props</div>

        <div class="panel-body">
            <div class="form-horizontal">

            <div class="form-group">
              <label for="type" class="control-label col-sm-3">type</label>
                <div  class="col-sm-9">
                  <select id="type" class="form-control" v-model="type">
                    <option>tree</option>
                    <option>cluster</option>
                  </select>
                </div>
            </div>

            <div class="form-group">
              <label for="layout-type" class="control-label col-sm-3">layoutType</label>
                <div  class="col-sm-9">
                  <select id="layout-type" class="form-control" v-model="layoutType">
                    <option>euclidean</option>
                    <option>circular</option>
                  </select>       
              </div>
            </div> 

            <div class="form-group">
              <label for="margin-x" class="control-label col-sm-3">marginx</label>
              <div class="col-sm-7">
                <input id="margin-x" class="form-control" type="range" min="0" max="200" v-model.number="Marginx">
              </div> 
                <div class="col-sm-2">
                  <p>{{Marginx}}px</p>       
              </div> 
            </div>        

            <div class="form-group">
              <label for="margin-y" class="control-label col-sm-3">marginy</label>
              <div class="col-sm-7">
                <input id="margin-y" class="form-control" type="range" min="0" max="200" v-model.number="Marginy">
              </div>
              <div class="col-sm-2">
                <p>{{Marginy}}px</p>       
              </div> 
            </div>   

             <div class="form-group">
              <label for="margin-y" class="control-label col-sm-3">radius</label>
              <div class="col-sm-7">
                <input id="margin-y" class="form-control" type="range" min="1" max="10" v-model.number="radius">
              </div>
              <div class="col-sm-2">
                <p>{{radius}}px</p>       
              </div> 
            </div>        

            <div class="form-group">
              <label for="velocity" class="control-label col-sm-3">Duration</label>
              <div class="col-sm-7">
                <input id="velocity" class="form-control" type="range" min="0" max="3000" v-model.number="duration">
              </div>
              <div class="col-sm-2">
                <p>{{duration}}ms</p>       
              </div>
            </div>

            <div class="form-group">
              <label for="zoomable" class="">Zoomable</label>
              <input id="zoomable" class="form-check-input" type="checkbox" v-model="zoomable">
            </div> 

            <div class="form-group feedback">
              <i v-show="isLoading" class="fa fa-spinner fa-spin fa-2x fa-fw"></i>
              <span v-if="currentNode">Current Node: {{currentNode.data.text}}</span>
              <span v-else>No Node selected.</span>            
            </div>  

            <button type="button" :disabled="!currentNode" class="btn btn-primary" @click="expandAll" data-toggle="tooltip" data-placement="top" title="Expand All from current">
            <i class="fa fa-expand" aria-hidden="true"></i>          
            </button>

            <button type="button" :disabled="!currentNode" class="btn btn-secondary" @click="collapseAll" data-toggle="tooltip" data-placement="top" title="Collapse All from current">
            <i class="fa fa-compress" aria-hidden="true"></i>            
            </button>

            <button type="button" :disabled="!currentNode" class="btn btn-success" @click="showOnly" data-toggle="tooltip" data-placement="top" title="Show Only from current">
            <i class="fa fa-search-plus" aria-hidden="true"></i>       
            </button>

            <button type="button" :disabled="!currentNode" class="btn btn-warning" @click="show" data-toggle="tooltip" data-placement="top" title="Show current">
            <i class="fa fa-binoculars" aria-hidden="true"></i>           
            </button>

            <button v-if="zoomable" type="button" class="btn btn-warning" @click="resetZoom" data-toggle="tooltip" data-placement="top" title="Reset Zoom">
            <i class="fa fa-arrows-alt" aria-hidden="true"></i>                             
            </button>
            
            <button type="button" class="btn btn-danger" @click="gremlins" data-toggle="tooltip" data-placement="top" :title="isUnderGremlinsAttack ? 'stop attack' :'unleash gremlins'">
            <i class="fa" :disabled="isUnderGremlinsAttack" :class="isUnderGremlinsAttack ? 'fa-exclamation-triangle':'fa-optin-monster'" aria-hidden="true"></i>
            </button>

        </div>
      </div>
    </div>


    <div class="panel panel-default">
        <div class="panel-heading">Events</div>

        <div class="panel-body log">
          <div v-for="(event,index) in events" :key="index">
            <p><b>Name:</b> {{event.eventName}} <b>Data:</b>{{event.data.text}}</p>
          </div>
        </div>
    </div>

  </div>

  <div class="col-md-9 panel panel-default">
    <tree ref="tree" :identifier="getId" :zoomable="zoomable" :data="Graph.tree" :node-text="nodeText"  :margin-x="Marginx" :margin-y="Marginy" :radius="radius" :type="type" :layout-type="layoutType" :duration="duration" class="tree" @clicked="onClick" @expand="onExpand" @retract="onRetract"/>
  </div>
  
  </div>
</template>

<script>
import {tree} from '../../src/'
import data from '../../data/data'
import {getGremlin} from './gremlinConfiguration'

Object.assign(data, {
  type: 'tree',
  layoutType: 'euclidean',
  duration: 750,
  Marginx: 30,
  Marginy: 30,
  radius: 3,
  nodeText: 'text',
  currentNode: null,
  zoomable: true,
  isLoading: false,
  isUnderGremlinsAttack: false,
  events: []
})

export default {
  name: 'app',
  data () {
    return data
  },
  components: {
    tree
  },
  methods: {
    do (action) {
      if (this.currentNode) {
        this.isLoading = true
        this.$refs['tree'][action](this.currentNode).then(() => { this.isLoading = false })
      }
    },
    getId (node) {
      return node.id
    },
    expandAll () {
      this.do('expandAll')
    },
    collapseAll () {
      this.do('collapseAll')
    },
    showOnly () {
      this.do('showOnly')
    },
    show () {
      this.do('show')
    },
    onClick (evt) {
      this.currentNode = evt.element
      this.onEvent('onClick', evt)
    },
    onExpand (evt) {
      this.onEvent('onExpand', evt)
    },
    onRetract (evt) {
      this.onEvent('onRetract', evt)
    },
    onEvent (eventName, data) {
      this.events.push({eventName, data: data.data})
    },
    resetZoom () {
      if (!this.$refs['tree']) {
        return
      }
      this.isLoading = true
      this.$refs['tree'].resetZoom().then(() => { this.isLoading = false })
    },
    gremlins () {
      if (this.isUnderGremlinsAttack) {
        this.horde.stop()
        return
      }

      this.duration = 20
      const changeLayout = () => { this.type = (this.type === 'tree') ? 'cluster' : 'tree' }
      const changeType = () => { this.layoutType = (this.layoutType === 'euclidean') ? 'circular' : 'euclidean' }
      const resetZoom = this.resetZoom.bind(this)
      const [treeDiv] = this.$el.getElementsByClassName('tree')
      const [gremlinsButton] = this.$el.getElementsByClassName('btn-danger')
      var horde = getGremlin(gremlinsButton, treeDiv, changeType, changeLayout, resetZoom)
      horde.after(() => { this.isUnderGremlinsAttack = false })
      horde.unleash()
      this.horde = horde
      this.isUnderGremlinsAttack = true
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}

.tree {
  height: 800px;
  width: 100%;
}

.graph-root {
  height: 800px;
  width: 100%;
}

.feedback{
  height: 50px;
  line-height: 50px;
  vertical-align: middle;
}

.log  {
  height: 200px;
  overflow-x: auto;
  overflow-y: auto;
  overflow: auto;
  text-align: left;
}
</style>
