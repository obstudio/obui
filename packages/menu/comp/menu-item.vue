<script>

module.exports = {
  props: {
    command: {
      type: Object,
      default: {},
    },
    mnemonic: {
      type: String,
      default: '',
    },
    binding: {
      type: String,
      default: '',
    },
    caption: {
      type: String,
      default: '',
    },
    context: {
      type: Object
    },
  },

  computed: {
    disabled() {
      if (!this.command.enabled) return
      return !this.$menuManager.parseArgument(this.command.enabled, this.context)
    },
    keybinding() {
      let binding = this.binding || this.command.bind
      if (!binding) return ''
      if (binding.charAt(0) === '!') binding = binding.slice(1)
      return binding.replace(/[a-z]+/g, word => {
        return word.charAt(0).toUpperCase() + word.slice(1)
      }).replace(/ /g, ', ')
    },
  },

  methods: {
    handleClick(event) {
      if (this.disabled) {
        event.stopPropagation()
      } else {
        this.$menuManager.executeCommand(this.command)
      }
    }
  },
}

</script>

<template>
  <div :class="['menu-item', { disabled }]" @click="handleClick">
    <span class="label">
      <ob-checkbox v-if="command.checked !== undefined"
        :value="$menuManager.parseArgument(command.checked, context)" @change="handleClick"/>
      {{ caption || command.name }}
      <template v-if="mnemonic"> (<span class="mnemonic">{{ mnemonic }}</span>)</template>
      <template v-if="command.ellipsis"> ...</template>
    </span>
    <span class="binding">{{ keybinding }}</span>
  </div>
</template>

<style lang="scss" scoped>

> .label {
  .marklet-checkbox {
    font-size: 12px;
    margin-right: 4px;
    vertical-align: .5px;
  }
}

</style>
