<template>
  <v-data-table :items="formsData" :headers="filteredHeaders" >
    <template v-slot:item="{ item }">
      <tr>
      <td>{{ item.title }}</td>
      <template v-if="item.avatarUrl">
        <td><v-img :src="item.avatarUrl" width="100" height="100"></v-img></td>
      </template>
      <template v-else-if="item.nIDFilesUrl">
        <td><v-img :src="item.nIDFilesUrl" width="100" height="100"></v-img></td>
      </template>
      <td v-else>{{ item.value }}</td>
    </tr>
    </template>
  </v-data-table>
</template>
<script>
export default {
  props: {
    formsData: []
  }
};
</script>