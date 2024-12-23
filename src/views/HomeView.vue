<template>
  <div class="contact-manager container-fluid py-5">
    <h1 class="display-4 text-center mb-5">Agenda de Contactos</h1>

    <div class="row g-3 mb-4">
      <div class="col-md-6">
        <div class="input-group">
          <span class="input-group-text bg-primary text-white">
            <i class="bi bi-filter"></i>
          </span>
          <select class="form-select" @change="toFilter = $event.target.value">
            <option value="">Todos los países</option>
            <option v-for="country in countries" :value="country" :key="country">{{country}}</option>
          </select>
        </div>
      </div>
      <div class="col-md-6">
        <div class="input-group">
          <span class="input-group-text bg-primary text-white">
            <i class="bi bi-search"></i>
          </span>
          <input type="search" class="form-control" placeholder="Buscar por Nombre o Correo Electrónico" 
                 @search="toSearch = $event.target.value">
        </div>
      </div>
    </div>

    <div class="card shadow">
      <div class="card-body">
        <form @submit.prevent="save()" class="mb-4">
          <div class="row g-3">
            <div class="col-md-2">
              <input type="text" class="form-control" placeholder="Nombre" v-model="newContact.name">
            </div>
            <div class="col-md-2">
              <input type="email" class="form-control" placeholder="Correo Electrónico" v-model="newContact.email">
            </div>
            <div class="col-md-2">
              <input type="text" class="form-control" placeholder="Dirección" v-model="newContact.address">
            </div>
            <div class="col-md-2">
              <input type="text" class="form-control" placeholder="Teléfono" v-model="newContact.phone">
            </div>
            <div class="col-md-2">
              <select class="form-select" v-model="newContact.country">
                <option value="" disabled selected>Seleccionar país</option>
                <option v-for="country in countries" :value="country" :key="country">{{country}}</option>
              </select>
            </div>
            <div class="col-md-2">
              <input type="text" class="form-control" placeholder="Ciudad" v-model="newContact.city">
            </div>
            <div class="col-12">
              <button type="submit" class="btn btn-primary w-100">Agregar Contacto</button>
            </div>
          </div>
        </form>

        <div class="table-responsive">
          <table class="table table-hover table-striped">
            <thead class="table-light">
                <tr>
                    <th>ID</th>
                    <th>Nombre</th>
                    <th>Correo Electrónico</th>
                    <th>Dirección</th>
                    <th>Teléfono</th>
                    <th>País</th>
                    <th>Ciudad</th>
                    <th>Acciones</th>
                </tr>
                <tr>
                    

                </tr>
            </thead>
            <tbody>
              <tr v-for="(link, index) in getList()" :key="index">
                <td>{{link.id}}</td>
                <td>{{link.name}}</td>
                <td>{{link.email}}</td>
                <td>{{link.address}}</td>
                <td>{{link.phone}}</td>
                <td>{{link.country}}</td>
                <td>{{link.city}}</td>
                <td>
                  <button type="button" class="btn btn-outline-primary btn-sm me-2" @click="edit(index)">
                    <i class="bi bi-pencil"></i>
                  </button>
                  <button type="button" class="btn btn-outline-danger btn-sm" @click="remove(index)">
                    <i class="bi bi-trash3"></i>
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>

    <!-- Modal -->
    <div class="modal fade" id="editModal" tabindex="-1" aria-labelledby="editModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header bg-primary text-white">
            <h5 class="modal-title" id="editModalLabel">Editar Contacto</h5>
            <button type="button" class="btn-close btn-close-white" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <form @submit.prevent="saveEdit()">
            <div class="modal-body" v-if="itemSelected">
              <div class="row g-3">
                <div class="col-md-6">
                  <label for="updateid" class="form-label">Identificador</label>
                  <input type="text" v-model="itemSelected.id" class="form-control bg-light" id="updateid" disabled>
                </div>
                <div class="col-md-6">
                  <label for="updateName" class="form-label">Nombre</label>
                  <input type="text" v-model="itemSelected.name" class="form-control" id="updateName">
                </div>
                <div class="col-md-6">
                  <label for="updateEmail" class="form-label">Correo Electrónico</label>
                  <div class="input-group">
                    <span class="input-group-text">@</span>
                    <input type="email" v-model="itemSelected.email" class="form-control" id="updateEmail">
                  </div>
                </div>
                <div class="col-md-6">
                  <label for="updatePhone" class="form-label">Teléfono</label>
                  <div class="input-group">
                    <span class="input-group-text"><i class="bi bi-telephone"></i></span>
                    <input type="tel" v-model="itemSelected.phone" class="form-control" id="updatePhone">
                  </div>
                </div>
                <div class="col-12">
                  <label for="updateAddress" class="form-label">Dirección</label>
                  <input type="text" v-model="itemSelected.address" class="form-control" id="updateAddress">
                </div>
                <div class="col-md-6">
                  <label for="updateCountry" class="form-label">País</label>
                  <select class="form-select" v-model="itemSelected.country" id="updateCountry">
                    <option v-for="country in countries" :value="country" :key="country">{{country}}</option>
                  </select>
                </div>
                <div class="col-md-6">
                  <label for="updateCity" class="form-label">Ciudad</label>
                  <input type="text" v-model="itemSelected.city" class="form-control" id="updateCity">
                </div>
              </div>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-outline-secondary" data-bs-dismiss="modal">Cancelar</button>
              <button type="submit" class="btn btn-primary">Guardar cambios</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
      data() {
          return {              
              countries: ["Argentina","Bolivia","Chile","Colombia","Brasil","Ecuador","Peru","Venezuela","Uruguay","USA", "Canada", "UK","Australia"],              
              newContact: {
                    id: 0,
                    name: "",
                    email: "",
                    address: "",
                    phone: "",
                    country: "",
                    city: ""
              },
              contactosArray: [
                  {
                    id: 1,
                    name: "Alice Johnson",
                    email: "alice.johnson@example.com",
                    address: "123 Maple Street",
                    phone: "123-456-7890",
                    country: "USA",
                    city: "New York"
                  },
                  {
                    id: 2,
                    name: "Bob Smith",
                    email: "bob.smith@example.com",
                    address: "456 Oak Avenue",
                    phone: "987-654-3210",
                    country: "Canada",
                    city: "Toronto"
                  },
                  {
                    id: 3,
                    name: "Carol White",
                    email: "carol.white@example.com",
                    address: "789 Pine Road",
                    phone: "555-123-4567",
                    country: "UK",
                    city: "London"
                  },
                  {
                    id: 4,
                    name: "David Brown",
                    email: "david.brown@example.com",
                    address: "321 Elm Street",
                    phone: "444-555-6666",
                    country: "Australia",
                    city: "Sydney"
                  },
                  {
                    id: 5,
                    name: "Emily Davis",
                    email: "emily.davis@example.com",
                    address: "654 Spruce Lane",
                    phone: "333-444-5555",
                    country: "USA",
                    city: "Los Angeles"
                  }
              ],              
              itemSelected: null,
              indexSelected: null,
              typeFilter: '',
              toFilter: '',
              toSearch: '',
              identificadorContador:5,
          }
      },
      methods: {
          save() {
              // Validar que los campos no estén vacíos
              if (this.newContact.name && this.newContact.email && this.newContact.address && this.newContact.phone && this.newContact.country && this.newContact.city) {
                this.identificadorContador++;
                this.newContact.id = this.identificadorContador;
                  // Agregar un nuevo objeto al array
                  /**
                   * const newObj {type: this.newlink.type}
                   * 
                   * const newObj = {...this.newLink}
                   * */
                  this.contactosArray.push({...this.newContact});
                  // Limpiar los campos del formulario
                  this.newContact.id = 0;                  
                  this.newContact.name = '';
                  this.newContact.email = '';
                  this.newContact.address = '';
                  this.newContact.phone = '';
                  this.newContact.country = '';
                  this.newContact.city = '';
              } else {
                  alert("Por favor, completa todos los campos.");
              }
          },
          remove(index) {
              if (confirm("¿Está seguro de eliminar este ítem?")) {
                  this.contactosArray.splice(index, 1);
              }
          },
          edit(index) {
              this.itemSelected = {...this.contactosArray[index]};
              this.indexSelected = index;
              const editModal = new bootstrap.Modal(document.getElementById('editModal'));
              editModal.show();
          },
          saveEdit() {
              this.contactosArray[this.indexSelected] = {...this.itemSelected};

              const modalElement = document.getElementById('editModal');
              const modalInstance = bootstrap.Modal.getInstance(modalElement) || new bootstrap.Modal(modalElement);
              modalInstance.hide();

              this.itemSelected = null;
              this.indexSelected = null;
          },
          getList() {
              let result = this.contactosArray.filter((item) => {
                  if (this.toSearch) {
                      return item.name.includes(this.toSearch) || item.email.includes(this.toSearch);
                  }
                  return true;
              });
              return result.filter((item) => {
                  if (this.toFilter) {
                      return item.country === this.toFilter;
                  }
                  return true;
              });              
          },  
      }
  }
</script>

<style scoped>
.contact-manager {
  max-width: 1200px;
  margin: 0 auto;
}
.table th, .table td {
  vertical-align: middle;
}
.btn-sm {
  padding: 0.25rem 0.5rem;
  font-size: 0.875rem;
}
</style>
