<script setup lang="ts">
defineProps({
  label: {
    type: String,
    default: 'Subscribe to our newsletter'
  },
  description: {
    type: String,
    default: 'Stay updated on new releases and features, guides, and community updates.'
  }
})

const toast = useToast()

const email = ref('')
const loading = ref(false)

function onSubmit() {
  if (loading.value) {
    return
  }
  loading.value = true

  $fetch('https://api.nuxt.com/newsletter/subscribe', {
    method: 'POST',
    body: { email: email.value }
  }).then(() => {
    toast.add({ title: 'Subscription pending', description: 'Please check your emails to confirm your subscription.', color: 'green' })
    email.value = ''
  }).catch((err) => {
    const error = JSON.parse(err.data?.message)
    const description = error[0].message || 'Something went wrong. Please try again later.'
    toast.add({ title: 'Subscription failed', description, color: 'red' })
  }).finally(() => {
    loading.value = false
  })
}
</script>

<template>
  <form @submit.prevent="onSubmit">
    <UFormGroup name="email" :label="label" size="lg" :description="description" :ui="{ label: { base: 'font-semibold' }, container: 'mt-3' }">
      <UInput
        v-model="email"
        type="email"
        placeholder="you@domain.com"
        :ui="{ icon: { trailing: { pointer: '', padding: { lg: 'px-1' } } } }"
        required
        autocomplete="off"
        class="max-w-sm"
      >
        <template #trailing>
          <UButton type="submit" size="xs" color="black" :label="loading ? 'Subscribing' : 'Subscribe'" :loading="loading" />
        </template>
      </UInput>
    </UFormGroup>
  </form>
</template>
