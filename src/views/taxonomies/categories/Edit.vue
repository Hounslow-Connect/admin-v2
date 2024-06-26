<template>
  <gov-width-container>
    <ck-loader v-if="loading" />
    <template v-else>
      <vue-headful
        :title="`${appName} - Edit Taxonomy Category: ${taxonomy.name}`"
      />

      <gov-back-link :to="{ name: 'admin-index-taxonomies' }"
        >Back to taxonomy categories</gov-back-link
      >
      <gov-main-wrapper>
        <gov-grid-row>
          <gov-grid-column width="one-half">
            <gov-heading size="xl">
              <gov-caption size="xl">Taxonomies</gov-caption>
              Categories
            </gov-heading>
            <gov-heading size="m">Edit category</gov-heading>
            <gov-body>
              From this page you can change the name of the taxonomy 'tags' on
              the site and how they relate to each other. These should not be
              changed without reviewing wider impact on the site
            </gov-body>

            <taxonomy-form
              :errors="form.$errors"
              :parent_id.sync="form.parent_id"
              :name.sync="form.name"
              :order.sync="form.order"
              @clear="form.$errors.clear($event)"
            />

            <gov-button v-if="form.$submitting" disabled type="submit"
              >Updating...</gov-button
            >
            <gov-button v-else @click="onSubmit" type="submit"
              >Update</gov-button
            >
            <ck-submit-error v-if="form.$errors.any()" />

            <gov-section-break size="l" />

            <ck-delete-button
              resource="category"
              :endpoint="`/taxonomies/categories/${this.taxonomy.id}`"
              @deleted="onDelete"
            />
          </gov-grid-column>
        </gov-grid-row>
      </gov-main-wrapper>
    </template>
  </gov-width-container>
</template>

<script>
import http from "@/http";
import Form from "@/classes/Form";
import TaxonomyForm from "@/views/taxonomies/categories/forms/TaxonomyForm";

export default {
  name: "EditTaxonomyCategory",
  components: { TaxonomyForm },
  data() {
    return {
      loading: false,
      taxonomy: null,
      form: null
    };
  },
  methods: {
    async fetchTaxonomy() {
      this.loading = true;

      const response = await http.get(
        `/taxonomies/categories/${this.$route.params.taxonomy}`
      );
      this.taxonomy = response.data.data;
      this.form = new Form({
        parent_id: this.taxonomy.parent_id,
        name: this.taxonomy.name,
        order: this.taxonomy.order
      });

      this.loading = false;
    },
    async onSubmit() {
      await this.form.put(`/taxonomies/categories/${this.taxonomy.id}`);
      this.$router.push({ name: "admin-index-taxonomies" });
    },
    onDelete() {
      this.$router.push({ name: "admin-index-taxonomies" });
    }
  },
  created() {
    this.fetchTaxonomy();
  }
};
</script>
