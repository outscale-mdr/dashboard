<script>
import KeyValue from '@shell/components/form/KeyValue';
import { ToggleSwitch } from '@components/Form/ToggleSwitch';

export default {
  components: {
    ToggleSwitch,
    KeyValue
  },

  props: {
    value: {
      type:     Object,
      required: true,
    },

    mode: {
      type:     String,
      required: true,
    },

    displaySideBySide: {
      type:    Boolean,
      default: false,
    },

    defaultContainerClass: {
      type:    String,
      default: '',
    },

    defaultSectionClass: {
      type:    String,
      default: '',
    },

    labelTitleTooltip: {
      type:    String,
      default: '',
    },

    annotationTitleTooltip: {
      type:    String,
      default: '',
    }
  },

  data() {
    return { toggler: false };
  },

  computed: {
    containerClass() {
      return `${ this.displaySideBySide ? 'row' : '' } ${ this.defaultContainerClass }`.trim();
    },

    sectionClass() {
      return `${ this.displaySideBySide ? 'col span-6' : 'row' } ${ this.defaultSectionClass }`.trim();
    },

  }
};
</script>
<template>
  <div :class="containerClass">
    <div class="labels">
      <div class="labels__header">
        <h3>
          <t k="labels.labels.title" />
        </h3>
        <ToggleSwitch
          v-if="value.hasSystemLabels"
          v-model="toggler"
          name="label-system-toggle"
          :on-label="t('labels.labels.show')"
        />
      </div>
      <p class="mt-10 mb-10">
        <t k="labels.labels.description" />
      </p>
      <div :class="sectionClass">
        <KeyValue
          key="labels"
          :value="value.labels"
          :protected-keys="value.systemLabels || []"
          :toggle-filter="toggler"
          :add-label="t('labels.addLabel')"
          :mode="mode"
          :read-allowed="false"
          :value-can-be-empty="true"
          @input="value.setLabels($event)"
        />
      </div>
    </div>
    <div class="spacer" />
    <div :class="sectionClass">
      <KeyValue
        key="annotations"
        :value="value.annotations"
        :add-label="t('labels.addAnnotation')"
        :mode="mode"
        :title="t('labels.annotations.title')"
        :title-protip="annotationTitleTooltip"
        :read-allowed="false"
        :value-can-be-empty="true"
        @input="value.setAnnotations($event)"
      />
    </div>
  </div>
</template>

<style lang="scss" scoped>
.labels {
  &__header {
    display: flex;
    justify-content: space-between;
  }
}
</style>
