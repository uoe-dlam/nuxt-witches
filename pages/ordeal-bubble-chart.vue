<template>
  <div>
    <div id="main-content" class="pl-5 pr-5 pt-3 pb-3">
      <div class="flex content-start items-center">
        <h1 class="mb-3">Trial Ordeals</h1>
      </div>
      <div>
        These bubble charts show the different trial ordeals that took place
        during the witchcraft trials. Trial by ordeal was a judicial practice in
        which the accused witches innocence was tested through an unpleasant
        experience such as witch-pricking. There are not many records of ordeals
        taking place during this period. It is likely that many of these
        procedures were not offically recorded.
      </div>
      <h2 class="mt-3">Trial Ordeal with Unknown Values</h2>
      <div>
        <iframe
          style="width: 80vw; height: 70vh; border: none"
          src="https://query.wikidata.org/embed.html#%23Ordeal%20of%20the%20accused%20witches%20in%20the%20Survey%20of%20Scottish%20witchcraft%0A%0A%23defaultView%3ABubbleChart%0ASELECT%20(IF(BOUND(%3Fordeal)%2C%20%3FordealLabel%2C%20%22unknown%22)%20as%20%3Flabel)%20%3Fcount%0A%3Fordeal%20%3FordealLabel%20%0A%0AWHERE%0A%7B%0A%20%20%7B%0A%20%20%20%20SELECT%20%3Fordeal%20(COUNT(%3Fperson)%20AS%20%3Fcount)%20WHERE%20%7B%0A%20%20%20%20%20%20%3Fperson%20wdt%3AP4532%20%3Faccused%20.%0A%20%20%20%20%20%20OPTIONAL%20%0A%20%20%20%20%20%20%7B%0A%20%20%20%20%20%20%3Fperson%20wdt%3AP7209%20%3Fordeal.%0A%20%20%20%20%20%20%7D%0A%20%20%20%20%7D%0A%20%20%20%20GROUP%20BY%20%3Fordeal%0A%20%20%7D%0A%20%20SERVICE%20wikibase%3Alabel%20%7B%20bd%3AserviceParam%20wikibase%3Alanguage%20%22%5BAUTO_LANGUAGE%5D%2Cen%22.%20%7D%0A%7D%0AORDER%20BY%20DESC(%3Fcount)%0ALIMIT%204000"
          referrerpolicy="origin"
          sandbox="allow-scripts allow-same-origin allow-popups"
        ></iframe>
      </div>
      <h2 class="mt-3">Trial Ordeal with Unknown Values omitted</h2>
      <iframe
        style="width: 80vw; height: 70vh; border: none"
        src="https://query.wikidata.org/embed.html#%23ORDEAL%0A%23defaultView%3ABubbleChart%0ASELECT%20%3FordealLabel%20(COUNT(%3Ftrial)%20as%20%3Fcount)%20%0AWHERE%20%0A%7B%0A%20%20%3Fitem%20wdt%3AP4532%20%3Ftrial.%0A%20%0A%20%20%3Fitem%20wdt%3AP7209%20%3Fordeal.%20%0A%20%20%0A%20%20%0A%20%20SERVICE%20wikibase%3Alabel%20%7B%20bd%3AserviceParam%20wikibase%3Alanguage%20%22%5BAUTO_LANGUAGE%5D%2Cen%22.%20%7D%0A%7D%0AGROUP%20BY%20%3FordealLabel%0A%0AORDER%20BY%20DESC%20(%3Fcount)"
        referrerpolicy="origin"
        sandbox="allow-scripts allow-same-origin allow-popups"
      ></iframe>
    </div>
  </div>
</template>

<script>
definePageMeta({
  layout: "default",
});

export default {
  data: () => ({}),
  methods: {},
  computed: {},
  mounted: function () {},
};
</script>

<style></style>
