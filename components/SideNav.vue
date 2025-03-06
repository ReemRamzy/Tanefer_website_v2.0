<template>
  <div
    id="w-nav-overlay-0"
    class="w-nav-overlay"
    data-wf-ignore=""
    style="height: 100%; display: block; width: 100%; z-index: 1000; background-color: rgba(0,0,0,0.2); top: 0;"
    @click.self="$emit('close')"
  >
    <nav
      role="navigation"
      class="nav-bar-flex w-nav-menu justify-space-between"
      data-nav-menu-open=""
      style="height: 8787.39px; transform: translateX(0px); transition: transform 400ms ease 0s;"
    >
      <div>
        <a
          href="https://flights.tanefer.com/"
          target="_blank"
          rel="noopener noreferrer"
          active-class="w--current"
          class="nav-link w-nav-link"
          @click="$emit('close')"
        >Flights</a>
        <NuxtLink to="/hotels" exact active-class="w--current" class="nav-link w-nav-link" @click="$emit('close')">
          Hotels
        </NuxtLink>
        <NuxtLink to="/cruises" exact active-class="w--current" class="nav-link w-nav-link" @click="$emit('close')">
          Nile cruises
        </NuxtLink>
        <NuxtLink to="/trips" exact active-class="w--current" class="nav-link w-inline-block" @click="$emit('close')">
          <div class="hot">
            <div>Pick & Custom your trip</div>
            <div class="hot-pointer" />
            <div class="hot-lable">
              HOT
            </div>
          </div>
        </NuxtLink>
        <NuxtLink to="/adventures" exact active-class="w--current" class="nav-link w-nav-link" @click="$emit('close')">
          Adventures
        </NuxtLink>
        <a
          href="https://tanefer.com/blog/"
          rel="noopener noreferrer"
          active-class="w--current"
          class="nav-link w-nav-link"
          @click="$emit('close')"
        >Blogs</a>
        <div :key="isAuthenticated">
          <div v-if="isAuthenticated" class="dropdown">
            <!-- <button class="dropdown-button">
              Hi, {{ username }}
            </button> -->
            <div class="dropdown-menu">
              <NuxtLink to="/profile" exact active-class="w--current" class="nav-link w-nav-link" @click="$emit('close')">
                Profile
              </NuxtLink>
              <NuxtLink to="/bookingHistory" exact active-class="w--current" class="nav-link w-nav-link" @click="$emit('close')">
                Booking History
              </NuxtLink>
              <NuxtLink to="/change-password" exact active-class="w--current" class="nav-link w-nav-link" @click="$emit('close')">
                Change Password
              </NuxtLink>
              <a exact active-class="w--current" class="nav-link w-nav-link" @click="logout">
                Logout
              </a>
            </div>
          </div>
          <div v-else class="login-container">
            <NuxtLink to="/login" exact active-class="w--current" class="nav-link w-nav-link" @click="$emit('close')">
              Login
            </NuxtLink>
          </div>
        </div>
      </div>
    </nav>
  </div>
</template>

<script>
export default {
  computed: {
    isAuthenticated () {
      return this.$store.getters['auth/isAuthenticated']
    },
    user () {
      return this.$store.getters['auth/user']
    },
    username () {
      return this.$store.getters['auth/user']?.username || 'User'
    }
  },
  methods: {
    async logout () {
      try {
        await this.$store.dispatch('auth/logout')
        this.$emit('close')
      } catch (error) {
      }
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
