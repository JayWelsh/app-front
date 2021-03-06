<template>
  <div>
    <ul class="my-2">
      <li class="font-bold">
        <base-text-input
          v-model="contact_.business_name"
          placeholder="Business name..."
          :rules="[isRequired()]"
          :disabled="!editable"
          required
        />
      </li>
      <li v-if="!!contact.contact_name || editable" class="mb-2 text-sm text-gray-700">
        <base-text-input
          v-model="contact_.contact_name"
          :disabled="!editable"
          placeholder="Contact name..."
        />
      </li>
      <div
        v-if="!addAddress && editable"
        class="blank-slate flex items-center justify-center bg-white border border-dashed border-gray-400 cursor-pointer text-gray-500 hover:text-green-500 hover:border-green-500 text-sm"
        @click="addAddressHandler"
      >
        <div class="flex items-center">
          <base-icon name="fas fa-plus" />
          <span class="ml-2">Address</span>
        </div>
      </div>
      <div v-else-if="editable || hasAddress" :class="['p-2 relative rounded', { 'bg-white border border-dashed border-gray-400': editable, 'bg-gray-100': !editable }]">
        <li v-if="editable || !!contact_.address_attributes.address_1" :class="{ 'pr-8': editable }">
          <base-text-input
            v-model="contact_.address_attributes.address_1"
            :disabled="!editable"
            :placeholder="`${$t('models.address.labels.address_1')}...`"
          />
        </li>
        <li v-if="editable || !!contact_.address_attributes.address_2" :class="{ 'pr-8': editable }">
          <base-text-input
            v-model="contact_.address_attributes.address_2"
            :disabled="!editable"
            :placeholder="`${$t('models.address.labels.address_2')}...`"
          />
        </li>
        <li v-if="editable || !!contact_.address_attributes.district" :class="{ 'pr-8': editable }">
          <base-text-input
            v-model="contact_.address_attributes.district"
            :disabled="!editable"
            :placeholder="`${$t('models.address.labels.district')}...`"
          />
        </li>
        <li v-if="editable || !!contact_.address_attributes.city" :class="{ 'pr-8': editable }">
          <base-text-input
            v-model="contact_.address_attributes.city"
            :disabled="!editable"
            :placeholder="`${$t('models.address.labels.city')}...`"
          />
        </li>
        <li v-if="editable || !!contact_.address_attributes.postcode" :class="{ 'pr-8': editable }">
          <base-text-input
            v-model="contact_.address_attributes.postcode"
            :disabled="!editable"
            :placeholder="`${$t('models.address.labels.postcode')}...`"
          />
        </li>
        <li v-if="editable || !!contact_.address_attributes.country" :class="{ 'pr-8': editable }">
          <base-select
            v-model="contact_.address_attributes.country"
            :placeholder="`${$t('models.address.labels.country')}...`"
            :options="countryOptions"
            :disabled="!editable"
            filterable
          />
        </li>
        <a
          :href="mapUrl"
          target="_blank"
          rel="noreferrer"
          class="text-gray-400 hover:text-blue-300"
        >
          <i class="fas fa-map-marked absolute top-0 right-0 m-2" />
        </a>
      </div>
    </ul>

    <div
      v-if="!addDetails && editable"
      class="blank-slate flex items-center justify-center bg-white border border-dashed border-gray-400 cursor-pointer text-gray-500 hover:text-green-500 hover:border-green-500 text-sm"
      @click="addDetails = true"
    >
      <div class="flex items-center">
        <base-icon name="fas fa-plus" />
        <span class="ml-2">Details</span>
      </div>
    </div>
    <div v-else class="flex -mx-2 text-xs">
      <div class="mx-2 flex-col text-gray-700 leading-tight">
        <div v-if="!!contact_.business_reg_number || editable" class="mb-2">
          {{ $t('labels.company_id') }}
        </div>
        <div v-if="!!contact_.tax_number || editable" class="mb-2">
          {{ $t('labels.tax_id') }}
        </div>
        <div v-if="!!contact_.email || editable" class="mb-2">
          {{ $t('labels.email') }}
        </div>
        <div v-if="!!contact_.phone || editable" class="mb-2">
          {{ $t('labels.phone') }}
        </div>
      </div>
      <div class="mx-2 flex-col flex-1">
        <div v-if="!!contact_.business_reg_number || editable">
          <base-text-input
            v-model="contact_.business_reg_number"
            :disabled="!editable"
            placeholder="Registration ID..."
            :mb="2"
          />
        </div>
        <div v-if="!!contact_.tax_number || editable">
          <base-text-input
            v-model="contact_.tax_number"
            :disabled="!editable"
            placeholder="Tax ID..."
            :mb="2"
          />
        </div>
        <div v-if="!!contact_.email || editable">
          <base-text-input
            v-model="contact_.email"
            :disabled="!editable"
            placeholder="Contact email address..."
            :mb="2"
          />
        </div>
        <div v-if="!!contact_.phone || editable">
          <base-text-input
            v-model="contact_.phone"
            :disabled="!editable"
            placeholder="Contact phone number..."
            :mb="2"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { isRequired } from '~/lib/validations'

export default {
  name: 'ContactDetails',

  props: {
    contact: { type: Object, required: true },
    editable: { type: Boolean, default: false }
  },

  data () {
    return {
      contact_: {},
      addAddress: false,
      addDetails: false
    }
  },

  computed: {
    hasAddress () {
      return this.contact_.address_attributes && Object.values(this.contact_.address_attributes).some(v => !!v)
    },

    mapUrl () {
      if (!this.hasAddress) return ''
      const attrs = this.contact_.address_attributes
      const addressParams = Object.keys(attrs).filter(k => k !== 'id' && !!attrs[k]).map(k => attrs[k]).join(',')
      return `https://www.google.com/maps?q=${addressParams}`
    },

    countryOptions () {
      const countriesMap = this.$t('options.countries')
      return Object.keys(countriesMap).map(k => ({ text: countriesMap[k], value: k }))
    },

    hasDetails () {
      return !!this.contact_.business_reg_number ||
             !!this.contact_.tax_number ||
             !!this.contact_.email ||
             !!this.contact_.phone
    }
  },

  watch: {
    contact_: {
      handler (newVal) {
        this.$emit('change', newVal)
      },
      deep: true
    }
  },

  beforeMount () {
    this.contact_ = Object.assign({}, this.contact)
    this.addAddress = this.editable && this.hasAddress
    this.addDetails = this.hasDetails
  },

  methods: {
    isRequired,

    addAddressHandler () {
      this.contact_.address_attributes = {}
      this.addAddress = true
    }
  }
}
</script>

<style lang="css" scoped>
.blank-slate {
  transition: all .3s ease;
}
</style>
