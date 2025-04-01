<template>
  <div class="w-full border-b h-16 px-3 flex gap-x-2 items-center bg-white">
    <a
      v-if="backButton"
      href="#"
      class="ml-2 flex text-blue font-semibold text-sm -m-1 hover:bg-blue-500/10 rounded-md p-1 group"
      @click.prevent="$emit('go-back')"
    >
      <Icon
        name="heroicons:arrow-left-20-solid"
        class="text-blue mr-1 w-6 h-6 group-hover:-translate-x-0.5 transition-all"
      />
    </a>

    <svg
      width="32"
      height="27"
      viewBox="0 0 32 27"
      fill="none"
      xmlns="http://www.w3.org/2000/svg"
      :style="{ filter: 'drop-shadow(0 0 .1rem #00000050)' }"
      class="mx-4 mr-8 h-10 w-10"
    >
      <path
        d="M27.6566 0.247077H26.8C26.8137 0.533268 26.8196 0.819458 26.8196 1.10761C26.8196 12.2514 17.7791 21.3154 6.69808 21.5722L7.74286 24.0695C8.97583 27.0196 12.7845 27.688 14.9153 25.326L30.8478 7.6547C33.402 4.8222 31.432 0.245117 27.6605 0.245117L27.6566 0.247077Z"
        fill="#487AE9"
      />
      <path
        d="M3.65576 14.2998C4.43984 14.439 5.31606 14.5115 6.1413 14.5115C13.6567 14.5115 19.7491 8.41918 19.7491 0.90374C19.7491 0.684197 19.7432 0.464653 19.7334 0.24707H26.7999C26.8137 0.533261 26.8195 0.819451 26.8195 1.1076C26.8195 12.2514 17.7791 21.3154 6.69801 21.5722L3.65772 14.2959"
        fill="#283CA5"
      />
      <path
        d="M6.14137 14.5115C13.6568 14.5115 19.7491 8.41918 19.7491 0.90374C19.7491 0.684197 19.7433 0.464653 19.7335 0.24707H4.42422C3.84988 0.24707 3.30887 0.354882 2.8149 0.546982C2.76786 0.564624 2.7208 0.584226 2.67376 0.603828C2.57967 0.644992 2.48558 0.688117 2.39541 0.735162C2.38953 0.737122 2.38561 0.741043 2.37973 0.743003C2.317 0.776326 2.25427 0.81161 2.19154 0.846894C2.03865 0.935103 1.8936 1.03311 1.75442 1.13896C1.70542 1.17621 1.65641 1.21541 1.60741 1.25462C0.180375 2.44054 -0.454732 4.487 0.358755 6.39624L2.47186 11.4595L3.65583 14.2978C4.43991 14.437 5.31612 14.5095 6.14137 14.5095V14.5115Z"
        fill="#202A5E"
      />
    </svg>

    <UTabs
      id="form-editor-navbar-tabs"
      v-model="activeTab"
      :items="[
        { label: 'Build' },
        { label: 'Design'},
        { label: 'Settings'}
      ]"
    />

    <div class="flex-grow flex justify-center">
      <EditableTag
        id="form-editor-title"
        v-model="form.title"
        element="h3"
        class="font-medium py-1 text-md w-48 text-gray-500 truncate form-editor-title"
      />
      <UBadge
        v-if="form.visibility == 'draft'"
        color="yellow"
        variant="soft"
        label="Draft"
      />
      <UBadge
        v-else-if="form.visibility == 'closed'"
        color="gray"
        variant="soft"
        label="Closed"
      />
    </div>

    <UndoRedo />

    <div
      class="flex items-stretch gap-x-2"
    >
      <UTooltip
        text="Help"
        class="items-center relative"
        :popper="{ placement: 'left' }"
      >
        <a
          v-track.form_editor_help_button_clicked
          href="#"
          class="text-sm p-2 hover:bg-gray-100 cursor-pointer rounded-lg text-gray-500 hover:text-gray-800 cursor-pointer"
          @click.prevent="crisp.openHelpdesk()"
        >
          <Icon
            name="heroicons:question-mark-circle"
            class="w-5 h-5"
          />
        </a>
      </UTooltip>
      <slot name="before-save" />
      <UTooltip :popper="{ placement: 'left' }">
        <template #text>
          <UKbd
            :value="metaSymbol"
            size="xs"
          />
          <UKbd
            value="s"
            size="xs"
          />
        </template>
        <UButton
          v-track.save_form_click
          color="primary"
          class="px-8 md:px-4 py-2"
          :loading="updateFormLoading"
          :class="saveButtonClass"
          @click="emit('save-form')"
        >
          <svg
            class="w-4 h-4 text-white inline mr-1 -mt-1"
            viewBox="0 0 24 24"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              d="M17 21V13H7V21M7 3V8H15M19 21H5C4.46957 21 3.96086 20.7893 3.58579 20.4142C3.21071 20.0391 3 19.5304 3 19V5C3 4.46957 3.21071 3.96086 3.58579 3.58579C3.96086 3.21071 4.46957 3 5 3H16L21 8V19C21 19.5304 20.7893 20.0391 20.4142 20.4142C20.0391 20.7893 19.5304 21 19 21Z"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
          <template v-if="form.visibility === 'public'">
            Publish Form
          </template>
          <template v-else>
            Save Changes
          </template>
        </UButton>
      </UTooltip>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { storeToRefs } from 'pinia'
import UndoRedo from '../../editors/UndoRedo.vue'
import { useWorkingFormStore } from '~/stores/working_form'
import { useCrisp } from '~/composables/useCrisp'

defineProps({
  backButton: {
    type: Boolean,
    default: true
  },
  updateFormLoading: {
    type: Boolean,
    required: true
  },
  saveButtonClass: {
    type: String,
    default: ''
  }
})

const emit = defineEmits(['go-back', 'save-form'])

const { metaSymbol } = useShortcuts()
defineShortcuts({
  meta_s: {
    handler: () => emit('save-form')
  }
})

const workingFormStore = useWorkingFormStore()
const crisp = useCrisp()

const form = computed(() => workingFormStore.content)
const { activeTab } = storeToRefs(workingFormStore)
</script>
