<template>
  <div :class="styles.arrayList.label">{{ control.label }}</div>
  <div :class="styles.arrayList.description">{{ control.description }}</div>

  <q-card v-if="control.visible" flat :class="styles.arrayList.root">
    <div v-for="(_, index) in control.data" :key="`${control.path}-${index}`" :class="styles.arrayList.itemWrapper">
      <array-list-element
        :move-up="moveUp?.(control.path, index)"
        :move-up-enabled="control.enabled && index > 0"
        :move-down="moveDown?.(control.path, index)"
        :move-down-enabled="control.enabled && index < control.data.length - 1"
        :copy-enabled="control.enabled"
        :copy="() => copy(index)"
        :delete-enabled="control.enabled && !minItemsReached"
        :delete="removeItems?.(control.path, [index])"
        :label="childLabelForIndex(index)"
        :styles="styles"
      >
        <dispatch-renderer
          :schema="control.schema"
          :uischema="childUiSchema"
          :path="composePaths(control.path, `${index}`)"
          :enabled="control.enabled"
          :renderers="control.renderers"
          :cells="control.cells"
        />
      </array-list-element>
    </div>

    <!--eslint-disable-next-line @intlify/vue-i18n/no-raw-text-->
    <div v-if="noData" :class="styles.arrayList.noData">no data</div>
  </q-card>
  <div align="right">
    <q-btn
      rounded
      unelevated
      color="grey"
      icon-right="sym_o_add"
      label="Add"
      :class="styles.arrayList.addButton"
      :disabled="!control.enabled || (appliedOptions.restrict && maxItemsReached)"
      @click="addButtonClick"
    />
  </div>
</template>

<script lang="ts">
import { composePaths, createDefaultValue, type ControlElement, Resolve, type JsonSchema } from '@jsonforms/core'
import { defineComponent } from 'vue'
import { DispatchRenderer, rendererProps, useJsonFormsArrayControl, type RendererProps } from '@jsonforms/vue'
import { useQuasarArrayControl } from '../util'
import ArrayListElement from './ArrayListElement.vue'

const controlRenderer = defineComponent({
  name: 'ArrayListRenderer',
  components: {
    ArrayListElement,
    DispatchRenderer,
  },
  props: {
    ...rendererProps<ControlElement>(),
  },
  setup(props: RendererProps<ControlElement>) {
    return useQuasarArrayControl(useJsonFormsArrayControl(props))
  },
  computed: {
    noData(): boolean {
      return !this.control.data || this.control.data.length === 0
    },
    arraySchema(): JsonSchema | undefined {
      return Resolve.schema(this.schema, this.control.uischema.scope, this.control.rootSchema)
    },
    maxItemsReached(): boolean | undefined {
      return (
        this.arraySchema !== undefined &&
        this.arraySchema.maxItems !== undefined &&
        this.control.data !== undefined &&
        this.control.data.length >= this.arraySchema.maxItems
      )
    },
    minItemsReached(): boolean | undefined {
      return (
        this.arraySchema !== undefined &&
        this.arraySchema.minItems !== undefined &&
        this.control.data !== undefined &&
        this.control.data.length <= this.arraySchema.minItems
      )
    },
  },
  methods: {
    composePaths,
    createDefaultValue,
    addButtonClick() {
      this.addItem(this.control.path, createDefaultValue(this.control.schema, this.control.rootSchema))()
    },
    copy(index: number) {
      const copy = JSON.parse(JSON.stringify(this.control.data[index]))
      copy.name = copy.name + ' - copy'
      this.addItem(this.control.path, copy)()
    },
  },
})

export default controlRenderer
</script>
