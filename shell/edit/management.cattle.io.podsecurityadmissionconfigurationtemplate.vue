<script lang="ts">
import Vue, { PropType, VueConstructor } from 'vue';
import CreateEditView from '@shell/mixins/create-edit-view';
import CruResource from '@shell/components/CruResource.vue';
import PodSecurityAdmission from '@shell/components/PodSecurityAdmission.vue';
import Loading from '@shell/components/Loading.vue';
import NameNsDescription from '@shell/components/form/NameNsDescription.vue';
import { PSA, PSAConfig, PSADefaults, PSAExemptions } from '@shell/types/pod-security-admission';
import { PSADimensions } from '@shell/config/pod-security-admission';
import { MANAGEMENT } from '@shell/config/types';

export default (Vue as VueConstructor<Vue & InstanceType<typeof CreateEditView>>).extend({
  mixins:     [CreateEditView],
  components: {
    CruResource,
    Loading,
    NameNsDescription,
    PodSecurityAdmission,
  },

  async fetch() {
    await this.$store.dispatch('management/findAll', { type: MANAGEMENT.PSA });
  },

  props: {
    value: {
      type:     Object as PropType<PSA>,
      required: true,
      default:  () => ({}) as PSA
    },

    mode: {
      type:     String,
      required: true,
    }
  },
  computed: {
    /**
     * Get or initialize exemptions if none
     */
    exemptions(): PSAExemptions {
      return this.value?.configuration?.exemptions || {};
    },

    /**
     * Get or initialize defaults labels if none
     */
    defaults(): PSADefaults {
      return this.value?.configuration?.defaults || {};
    },
  },

  methods: {
    /**
     * Replace exemption with new values
     */
    setExemptions(exemptions: PSAExemptions) {
      this.value.configuration.exemptions = exemptions;
    },

    /**
     * Replace defaults with new values
     */
    setDefaults(labels: PSADefaults) {
      this.value.configuration.defaults = labels;
    },
  },

  created() {
    // Initialize configuration if none, as default value resource case
    if (!this.value.configuration) {
      this.value.configuration = {
        defaults:   {},
        exemptions: Object.assign({}, ...PSADimensions.map(dimension => ({ [dimension]: [] }))),
      } as PSAConfig;
    }
  }
});
</script>

<template>
  <Loading v-if="$fetchState.pending" />
  <CruResource
    v-else
    :mode="mode"
    :resource="value"
    :errors="errors"
    :cancel-event="true"
    @error="setErrors"
    @finish="save"
    @cancel="done()"
  >
    <NameNsDescription
      :value="value"
      :namespaced="false"
      :mode="mode"
    />
    <PodSecurityAdmission
      :labels="defaults"
      :labels-always-active="true"
      :exemptions="exemptions"
      :mode="mode"
      @updateLabels="setDefaults($event)"
      @updateExemptions="setExemptions($event)"
    />
  </CruResource>
</template>
