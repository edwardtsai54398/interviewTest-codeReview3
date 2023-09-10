<script >
import {defineComponent, computed, ref} from 'vue'
      import { isEmpty } from 'lodash-es'
      // import { isFieldError } from '@/components/form/GDBFormItem.vue'
      //不確定 GDBFormItem.vue 是怎樣的功能，題目沒寫

      export default defineComponent({
        name: 'GDBInput',
        props: {
          modelValue: {
            type: [String, Number],
            default: null,
          },
          type: {
            type: String,
            default: 'text', // text | password | number
          },
          placeholder: {
            type: String,
            default: '',
          },
          prefix: {
            type: String,
            default: '',
          },
          suffix: {
            type: String,
            default: '',
          },
          max: {
            type: Number,
            default: Infinity,
          },
          min: {
            type: Number,
            default: -Infinity,
          },
          hasDecimalPoint: {
            type: Boolean,
            default: false,
          },
          size: {
            type: String,
            default: 'medium',
          },
          fullWidth: {
            type: Boolean,
            default: false,
          },
          emptyToNull: {
            type: Boolean,
            default: true,
          },
          disabled: {
            type: Boolean,
            default: false,
          },
        },
        emits: ['update:modelValue', 'change'],
        setup(props, { emit }) {
          const value = computed({
            get: () => props.modelValue,
            set: (val) => {
              switch (true) {
                case props.disabled:
                  break
                case props.type === 'number':
                  if (val === '') {
                    emit('update:modelValue', val)
                    return
                  }
                  emit('update:modelValue', Number(val))
                  break
                case props.emptyToNull && val === '':
                  emit('update:modelValue', null)
                  break
                default:
                  emit('update:modelValue', val)
                  break
              }
            },
          })

          const isShowPassword = ref(false)
          const isFocus = ref(false)

          // const isError = inject(isFieldError, readonly(computed(() => false)))

          const state = computed(() => {
            return {
              inputType: props.type === 'password' ? (isShowPassword.value ? 'text' : 'password') : props.type,
              // isError: isError.value,
              isDisabled: props.disabled,
              hasPrefix: !isEmpty(props.prefix),
              hasSuffix: !isEmpty(props.suffix),
            }
          })

          const changeEvent = () => {
            !props.disabled && emit('change', value.value)
          }

          const focusEvent = () => {
            isFocus.value = true
          }

          const blurEvent = () => {
            const { max, min, hasDecimalPoint, type } = props
            isFocus.value = false

            if (type !== 'number') return
            if (value.value === '') value.value = 0
            if (!hasDecimalPoint) value.value = Math.floor(Number(value.value))
            if (Number(value.value) < min) value.value = min
            if (Number(value.value) > max) value.value = max

          }

          return {
            value,
            isShowPassword,
            isFocus,
            state,
            changeEvent,
            focusEvent,
            blurEvent,
          }
        },
      })
    
</script>

<template>
  
      <div
        class="gdb-input"
        :class="{
          [`gdb-input--${size}`]: true,
          // 'gdb-input--error': state.isError,
          'gdb-input--password': state.inputType === 'password',
          'gdb-input--disabled': state.isDisabled,
          'gdb-input--full': fullWidth,
        }"
      >
        <div class="gdb-input__inner" :class="{ 'gdb-input__inner--focus': isFocus }">
          <div v-if="state.hasPrefix" class="gdb-input__prefix">{{ prefix }}</div>
          <div class="gdb-input__input-wrapper">
            <input
              v-model="value"
              :type="state.inputType"
              class="gdb-input__input"
              :placeholder="placeholder"
              :disabled="state.isDisabled"
              @focus="focusEvent"
              @blur="blurEvent"
              @change="changeEvent"
            />
            <div v-if="type === 'password'" class="gdb-input__password" @click="isShowPassword = !isShowPassword">
              <span v-if="isShowPassword" class="gdb-input__password-icon material-icons-round">
                <font-awesome-icon icon="fa-solid fa-eye" />
              </span>
              <span v-else class="gdb-input__password-icon material-icons-round">
                <font-awesome-icon icon="fa-solid fa-eye-slash" />
              </span>
            </div>
          </div>
          <div v-if="state.hasSuffix" class="gdb-input__suffix">{{ suffix }}</div>
        </div>
      </div>
    
</template>

<style lang="scss" scoped>
@import "@/assets/scss/all.scss";
</style>
