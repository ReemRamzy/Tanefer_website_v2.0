<template>
  <div
    v-if="isOpen"
    id="w-nav-overlay-0"
    class="w-nav-overlay"
    data-wf-ignore=""
    style="height: 100%; display: block; width: 100%; z-index: 1000; background-color: rgba(0,0,0,0.2); top: 0;"
    @click.self="closeMenu"
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
          class="nav-link w-nav-link"
          @click="closeMenu"
        >Flights</a>

        <NuxtLink to="/hotels" exact class="nav-link w-nav-link" @click.native="closeMenu">
          Hotels
        </NuxtLink>

        <NuxtLink to="/cruises" exact class="nav-link w-nav-link" @click.native="closeMenu">
          Nile cruises
        </NuxtLink>

        <NuxtLink to="/trips" exact class="nav-link w-inline-block" @click.native="closeMenu">
          <div class="hot">
            <div>Pick & Custom your trip</div>
            <div class="hot-pointer" />
            <div class="hot-lable">
              HOT
            </div>
          </div>
        </NuxtLink>

        <NuxtLink to="/adventures" exact class="nav-link w-nav-link" @click.native="closeMenu">
          Adventures
        </NuxtLink>

        <a
          href="https://tanefer.com/blog/"
          rel="noopener noreferrer"
          class="nav-link w-nav-link"
          @click="closeMenu"
        >Blogs</a>

        <div :key="isAuthenticated">
          <div v-if="isAuthenticated" class="dropdown">
            <div class="dropdown-menu">
              <NuxtLink to="/profile" exact class="nav-link w-nav-link" @click.native="closeMenu">
                Profile
              </NuxtLink>

              <NuxtLink to="/bookingHistory" exact class="nav-link w-nav-link" @click.native="closeMenu">
                Booking History
              </NuxtLink>

              <NuxtLink to="/change-password" exact class="nav-link w-nav-link" @click.native="closeMenu">
                Change Password
              </NuxtLink>

              <a class="nav-link w-nav-link" @click="logout">
                Logout
              </a>
            </div>
          </div>

          <div v-else class="login-container">
            <NuxtLink to="/login" exact class="nav-link w-nav-link" @click.native="closeMenu">
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
  data () {
    return {
      isOpen: true // Menu starts open
    }
  },
  computed: {
    isAuthenticated () {
      return this.$store.getters['auth/isAuthenticated']
    },
    username () {
      return this.$store.getters['auth/user']?.username || 'User'
    }
  },
  methods: {
    closeMenu () {
      this.isOpen = false
    },
    async logout () {
      try {
        await this.$store.dispatch('auth/logout')
        this.closeMenu()
      } catch (error) {}
    }
  }
}
</script>
