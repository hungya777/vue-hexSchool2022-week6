<template>
  <header class="container mt-4">
    <h1 class="text-center">前台</h1>
    <nav class="navbar navbar-expand-lg bg-light">
      <div class="container-fluid">
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav me-auto mb-2 mb-lg-0">
            <li class="nav-item">
              <router-link class="nav-link" to="/">首頁</router-link>
            </li>
            <li class="nav-item">
              <router-link class="nav-link" to="/products">產品列表(前台)</router-link>
            </li>
            <li class="nav-item">
              <router-link class="nav-link" to="/cart">購物車(前台)</router-link>
            </li>
          </ul>
        </div>
      </div>
    </nav>
  </header>
  <router-view></router-view>
</template>

<script>
export default {
  data () {
    return {
    }
  }
}
</script>