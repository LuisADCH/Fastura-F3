<template>
  <template>
  <div>
    <div class="category-section">
      <h2>Categorías</h2>
    </div>
    <div class="product-section">
      <h2>Productos</h2>
      <input type="text" v-model="searchQuery" placeholder="Buscar...">
    </div>

    <div class="provider-section">
      <h2>Proveedores</h2>
    </div>
  </div>
</template>

<style scoped>
/* Define tu clase de estilo para los botones */
.custom-button {
  background-color: #4CAF50;
  border: none;
  color: white;
  padding: 10px 20px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  transition-duration: 0.4s;
  cursor: pointer;
}

.custom-button:hover {
  background-color: #45a049;
}
</style>

<script>
  import { required, minLength } from 'vuelidate/lib/validators';

export default {
  data() {
    return {
      formData: {
        name: '',
        email: ''
      }
    };
  },
  validations: {
    formData: {
      name: { required, minLength: minLength(3) },
      email: { required, email }
    }
  },
  methods: {
    submitForm() {
      this.$v.formData.$touch();
      if (!this.$v.formData.$error) {
        // Enviar datos al servidor
      }
    }
  }
}
  data() {
    return {
        searchQuery: ''
    };
},
computed: {
    filteredItems() {
        return this.items.filter(item => {
            return item.name.toLowerCase().includes(this.searchQuery.toLowerCase());
        });
    }
}
export default {
  data() {
    return {
    };
  },
  methods: {
  async fetchData() {
    try {
      const response = await axios.get('/api/items');
      this.items = response.data;
    } catch (error) {
      console.error('Error al cargar los datos:', error);
      this.errorMessage = 'Error al cargar los datos. Inténtelo de nuevo más tarde.';
    }
  }
}
  methods: {
  }
}
</script>

<style scoped>
.category-section {
  margin-bottom: 20px;
}

.product-section {
  margin-bottom: 20px;
}

.provider-section {
  margin-bottom: 20px;
}
</style>
  <div>
    <div class="page-header pr-0">
      <h2>
        <a href="/dashboard">
          <i class="fas fa-tachometer-alt"></i>
        </a>
      </h2>
      <ol class="breadcrumbs">
        <li class="active">
          <span>Productos</span>
        </li>
      </ol>
      <div class="right-wrapper pull-right">
        <template>
          
          <!-- v-if="typeUser === 'admin'" -->
          <!-- <button type="button" class="btn btn-custom btn-sm  mt-2 mr-2" @click.prevent="clickImport()"><i class="fa fa-upload"></i> Importar</button>-->
          <button
            type="button"
            class="btn btn-custom btn-sm mt-2 mr-2"
            @click.prevent="clickCreate()"
          >
            <i class="fa fa-plus-circle"></i> Nuevo
          </button>
        </template>
      </div>
    </div>
    <div class="card mb-0">
      <div class="card-header bg-info">
        <h3 class="my-0">Listado de productos Restaurante</h3>
      </div>
      <div class="card-body">
        <data-table :resource="'items'">
          <tr slot="heading" width="100%">
            <th>#</th>
            <th>Cód. Interno</th>
            <th>Unidad</th>
            <th>Nombre</th>
            <th>Descripción</th>
            <th class="text-center">P.Unitario (Venta)</th>
            <th class="text-center">Stock General</th>
            <th class="text-center">Imagen</th>
            <th class="text-center">Categoría</th>
            <th class="text-center">Proveedores</th>
            <th class="text-center">Visible en Restaurant</th>
            <th class="text-right">Acciones</th>
          </tr>
          <tr></tr>
          <tr slot-scope="{ index, row }">
            <td>{{ index }}</td>
            <td>{{ row.internal_id }}</td>
            <td>{{ row.unit_type_id }}</td>
            <td>{{ row.description }}</td>
            <td>{{ row.name }}</td>
            <td class="text-center">{{ row.sale_unit_price }}</td>
            <td class="text-center">{{ stock(row.warehouses) }}</td>
            <td class="text-center">
              <a @click="viewImages(row)" href="#">
                <img :src="row.image_url_small" alt width="150" height="150" />
              </a>

              <!--<img :src="row.image_url_medium"  width="40" height="40" class="img-thumbail img-custom" /> -->
            </td>
            <td>
              {{row.category_description}}
            </td>
            <td> 
              <ul>
                <li v-for="provider in row.providers" :key="provider.id">
                  {{ provider.name }}
                </li>
              </ul>
            </td>
            <td class="text-center">
              <el-checkbox
                size="medium"
                @change="visibleRestaurant($event, row.id)"
                v-model="row.apply_restaurant"
              ></el-checkbox>
            </td>
            <td class="text-right">
              <template>
                <!-- v-if="typeUser === 'admin'" -->
                <button
                  type="button"
                  class="btn waves-effect waves-light btn-xs btn-info"
                  @click.prevent="clickCreate(row.id)"
                >Editar</button>
                <button
                  type="button"
                  class="btn waves-effect waves-light btn-xs btn-danger"
                  @click.prevent="clickDelete(row.id)"
                >Eliminar</button>
              </template>
            </td>
          </tr>
        </data-table>
      </div>

      <items-form :showDialog.sync="showDialog" :recordId="recordId"></items-form>

      <!-- <items-import :showDialog.sync="showImportDialog"></items-import> -->

      <warehouses-detail :showDialog.sync="showWarehousesDetail" :warehouses="warehousesDetail"></warehouses-detail>

      <!-- <images-record :showDialog.sync="showImageDetail" :recordImages="recordImages"></images-record> -->

      <el-dialog
        :visible.sync="showImageDetail"
        title="Imagenes de Producto"
        width="50%"
        append-to-body
        top="7vh"
      >
        <div class="row d-flex align-items-end justify-content-end">
          <div class="col-md-3">
            <h4>Thumbs</h4>
            <img class="img-thumbnail" :src="recordImages.image_url_small" alt width="128" />
          </div>
          <div class="col-md-4">
            <h4>Para productos de Venta</h4>
            <img class="img-thumbnail" :src="recordImages.image_url_medium" alt width="256" />
          </div>
          <div class="col-md-4">
            <h4>Para Tienda</h4>
            <img class="img-thumbnail" :src="recordImages.image_url" alt width="512" />
          </div>
        </div>
        <div class="row text-right pt-2">
          <div class="col align-self-end">
            <el-button type="primary" @click="showImageDetail = false">Cerrar</el-button>
          </div>
        </div>
      </el-dialog>
    </div>
  </div>
</template>
<script>
import ItemsForm from "../../../../../../../resources/js/views/tenant/items_ecommerce/form.vue";
import WarehousesDetail from "../../../../../../../resources/js/views/tenant/items_ecommerce/partials/warehouses.vue";
// import ItemsImport from './import.vue'
import DataTable from "../../../../../../../resources/js/components/DataTable.vue";
import { deletable } from "../../../../../../../resources/js/mixins/deletable";

export default {
  props: [], //'typeUser'
  mixins: [deletable],
  components: { ItemsForm, DataTable, WarehousesDetail }, //ItemsImport
  data() {
    return {
      showDialog: false,
      showImportDialog: false,
      showWarehousesDetail: false,
      showImageDetail: false,
      resource: "restaurant",
      recordId: null,
      warehousesDetail: [],
      recordImages: {
        image_url: "",
        image_url_medium: "",
        image_url_small: ""
      }
    };
  },
  created() {},
  methods: {
    viewImages(row) {
      this.recordImages.image_url = row.image_url;
      this.recordImages.image_url_medium = row.image_url_medium;
      this.recordImages.image_url_small = row.image_url_small;
      this.showImageDetail = true;
    },
    visibleRestaurant(apply_restaurant, id) {
      this.$http
        .post(`/${this.resource}/items/visible`, { id, apply_restaurant })
        .then(response => {
          if (response.data.success) {
            if (apply_restaurant) {
              this.$message.success(response.data.message);
            } else {
              this.$message.warning(response.data.message);
            }
            this.$eventHub.$emit("reloadData")

          } else {
            this.$message.error(response.data.message);
            this.$eventHub.$emit("reloadData")

          }
        })
        .catch(error => {})
        .then(() => {});
    },
    clickWarehouseDetail(warehouses) {
      this.warehousesDetail = warehouses;
      this.showWarehousesDetail = true;
    },
    clickCreate(recordId = null) {
      this.recordId = recordId;
      this.showDialog = true;
    },
    clickImport() {
      this.showImportDialog = true;
    },
    clickDelete(id) {
      this.destroy(`/items/${id}`).then(() =>
        this.$eventHub.$emit("reloadData")
      );
    },
    stock (items) {
      let stock = 0
      items.forEach((item) => {
        stock += parseInt(item.stock)
      })
      return stock
    }
  }
}
</script>
