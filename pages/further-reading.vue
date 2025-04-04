<template>
  <div id="main-content" class="text-wrapper">
    <h1 class="text-center">Further Reading</h1>
    <p class="text-justify">
      The following list of works, curated by
      <a
        href="https://www.ed.ac.uk/profile/julian-goodare"
        target="_blank"
        class="link"
        >Professor Julian Goodare</a
      >, a lecturer in Scottish History and Project Director of the Survey of
      Scottish Witchcraft, along with Ellie Whitehead, Assistant Wikimedian in
      Residence and and MScR researcher at the University of Edinburgh, is
      highly recommended for anyone seeking to learn more about history of the
      Scottish witch trials.
    </p>
    <div
      v-for="(book, index) in books"
      :key="index"
      class="flex justify-between"
    >
      <div>
        <p class="mt-6 text-lg font-medium">
          {{ book.title }}
        </p>
        <p class="mb-0">{{ book.author }}</p>
        <p>
          Access Here:
          <a class="hover:text-gray-500 underline" :href="book.link">{{
            book.name
          }}</a>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
definePageMeta({
  layout: "basic",
});

export default {
  data: () => ({
    books: [
      {
        title: "Satan and the Scots: The Devil in Post-Reformation, c1560-1700",
        author: "Michelle D.Brock (Farnham: Ashgate, 2016)",
        link: "https://www.taylorfrancis.com/books/mono/10.4324/9781315607627/satan-scots-michelle-brock",
        name: "Link to this work on the Taylor Francis webiste",
      },
      {
        title: "The Scottish Witch-Hunt in Context",
        author:
          "Julian Goodare (Manchester: Manchester University Press, 2002)",
        link: "https://manchesteruniversitypress.co.uk/9780719060243/",
        name: "Link to this work on the Manchester University Press website",
      },
      {
        title: "Scottish Witches and Witch-Hunters",
        author: "Julian Goodare (Basingloke: Palgrave Macmillan, 2013)",
        link: "https://link.springer.com/book/10.1057/9781137355942",
        name: "Link to this work on the Springer website",
      },
      {
        title: "Witchcraft and Belief in Early Modern Scotland",
        author:
          "Julian Goodare, Lauren Martin and Joyce Miller eds. (Basingloke: Palgrave Macmillan, 2008)",
        link: "https://link.springer.com/book/10.1057/9780230591400",
        name: "Link to this work on the Springer website",
      },
      {
        title:
          "Witchcraft and Folk Belief in the Age of Enlightenment: Scotland, 1670-1740",
        author: "Lizanne Henderson (Basingloke: Palgrave Macmillan, 2016)",
        link: "https://archive.org/details/witchcraftfolkbe0000hend",
        name: "Link to this work on the Internet Archive website",
      },
      {
        title: "Enemies of God: The Witch-Hunt in Scotland",
        author: "Christina Larner (London: Chatto & Windus, 1981)",
        link: "https://archive.org/details/enemiesofgodwitc00larn_0",
        name: "Link to this work on the Internet Archive website",
      },
      {
        title: "Witch-Hunting in Scotland: Law, Politics and Religion",
        author: "Brian P. Levack (London: Routledge, 2008)",
        link: "https://www.taylorfrancis.com/books/mono/10.4324/9780203089507/witch-hunting-scotland-brian-levack",
        name: "Link to this work on the Taylor Francis webiste",
      },
      {
        title: "The Witches of Fife: Witch-Hunting in a Scottish Shire",
        author: "Stuart Macdonald (East Linton: Tuckwell, 2001)",
        link: "https://archive.org/details/witchesoffifewit0000macd",
        name: "Link to this work on the Internet Archive website",
      },
      {
        title:
          "The Visions of Isobel Gowdie: Magic, Witchcraft and Dark Shamanism in Seventeenth-Century Scotland",
        author: "Emma Wilby (Brighton: Sussex Academic Press, 2010)",
        link: "https://archive.org/details/emma-wilby-the-visions-of-isobel-gowdie-magic-witchcraft-and-dark-shamanism-in-s",
        name: "Link to this work on the Internet Archive website",
      },
      {
        title: "Witches of the North: Scotland and Finmark",
        author: "Liv Helene Willumson (Leiden: Brill, 2013)",
        link: "https://brill.com/display/title/24241",
        name: "Link to this work on the BRILL website",
      },
      {
        title:
          "Witchcraft in Early Modern Scotland: James VI's Demonology and the North Berwick Witches ",
        author:
          "Lawrence Normand and Gareth Roberts  (Liverpool: Liverpool University Press, 2000)",
        link: "https://www.jstor.org/stable/j.ctt5vjmvw",
        name: "Link to this work on the JSTOR website",
      },
      {
        title: "Witch Hunt Podcast",
        author:
          "Presented by Louise Yeoman and Susan Morrison (BBC Scotland, 2019)",
        link: "https://www.bbc.co.uk/programmes/p07rn38z/episodes/player",
        name: "Link to this work on the BBC website",
      },
      {
        title: "Lucy Worsley Investigates: The Witch Hunts, ",
        author: "Presented by Lucy Worsley (BBC, 2022)",
        link: "https://www.bbc.co.uk/programmes/p0bvhk3w",
        name: "Link to this work on the BBC website",
      },
    ],
  }),
  methods: {},
  computed: {},
  mounted: function () {},
};
</script>

<style></style>
