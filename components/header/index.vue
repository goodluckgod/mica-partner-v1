<template>
  <div class="bg-white">
    <div class="bg-amber-50/50 p-3" v-if="$auth.user.stores[0].status == 'UNDER_REVIEW'">
      <div class="container mx-auto flex items-center justify-between">
        <p class="font-medium text-amber-500">Mağazanız Onaya Gönderildi</p>
        <p class="text-gray-500 text-sm font-light">
          En kısa süre içerisinde başvurunuz işleme alınacaktır. Gerekli
          durumlarda telefon ile aranabilirsiniz.
        </p>
      </div>
    </div>
    <div class="border-b border-gray-100">
      <div class="container flex items-center justify-between px-5 py-2">
        <ul>
          <li>
            <nuxt-link to="/">
              <logo-dark class="h-7" />
            </nuxt-link>
          </li>
        </ul>
        <ul class="flex items-center gap-3 text-xs font-light text-gray-500">
          <li>Yardım Merkezi</li>
          <li>Sıkça Sorulan Sorular</li>
          <li>Takvim</li>
        </ul>
      </div>
    </div>
    <div class="border-b border-gray-100">
      <div
        class="flex items-center justify-between container mx-auto px-5 py-2"
      >
        <menu-primary @menu="$emit('menu')" @unmenu="$emit('unmenu')" />
        <menu-user @menu="$emit('menu')" @unmenu="$emit('unmenu')" />
      </div>
    </div>
  </div>
</template>
