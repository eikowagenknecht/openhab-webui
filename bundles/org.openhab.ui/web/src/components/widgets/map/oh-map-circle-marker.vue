<template>
  <l-circle ref="marker" v-if="center && radius" :key="markerKey" :lat-lng="center" :radius="radius" v-bind="markerConfig" @update:latLng="$emit('update', $event)" @click="performAction">
    <l-tooltip v-if="config.label">
      {{ config.label }}
    </l-tooltip>
  </l-circle>
</template>

<script>
import { LCircle, LTooltip } from 'vue2-leaflet'

import mixin from '../widget-mixin'
import { actionsMixin } from '../widget-actions'
import { OhMapCircleMarkerDefinition } from '@/assets/definitions/widgets/map'

export default {
  mixins: [mixin, actionsMixin],
  components: {
    LCircle,
    LTooltip
  },
  widget: OhMapCircleMarkerDefinition,
  data () {
    return {
      markerKey: this.$f7.utils.id()
    }
  },
  computed: {
    center () {
      if (this.config.item) {
        const itemState = this.context.store[this.config.item]
        if (itemState && itemState.state.indexOf(',') > 0) {
          return itemState.state.split(',')
        }
      }
      if (this.config.location) {
        return this.config.location.split(',')
      }
      return null
    },
    radius () {
      if (this.config.radiusItem) {
        const itemState = this.context.store[this.config.radiusItem]
        if (itemState && !isNaN(parseFloat(itemState.state))) {
          return parseFloat(itemState.state)
        }
      }
      if (this.config.radius) {
        return parseFloat(this.config.radius)
      }
      return null
    },
    markerConfig () {
      if (!this.config) return {}
      let ret = {}
      Object.assign(ret, this.config)
      delete ret.latLng
      delete ret.radius
      return ret
    }
  },
  mounted () {
    this.$emit('update', this.center, this.radius)
  }
}
</script>
