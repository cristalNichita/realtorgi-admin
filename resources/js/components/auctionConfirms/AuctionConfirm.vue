<template>
  <section class="content">
    <div class="container-fluid">
      <div class="row">
        <div class="col-12">
          <div class="card">
            <div class="card-header">
              <h3 class="card-title">
                Список заявок на участие в лотах
              </h3>
            </div>
            <!-- /.card-header -->
            <div class="card-body table-responsive p-0">
              <table class="table table-hover">
                <thead>
                  <tr>
                    <th>ID</th>
                    <th>Пользователь</th>
                    <th>Лот</th>
                    <th>Статус подтверждения</th>
                    <th class="text-center">Действия</th>
                  </tr>
                </thead>
                <tbody>
                  <tr
                    v-for="auctionConfirm in auctionConfirms.data"
                    :key="auctionConfirm.id"
                  >
                    <td>{{ auctionConfirm.id }}</td>
                    <td v-if="auctionConfirm.user.full_name != '' && auctionConfirm.user.full_name != null">
                        <a style="cursor: pointer; color: rgb(6 103 249);" @click="modalUser(auctionConfirm)">
                            <b>{{ auctionConfirm.user.full_name }}</b>
                        </a>
                    </td>
                    <td v-else>
                        <a style="cursor: pointer; color: rgb(6 103 249);" @click="modalUser(auctionConfirm)">
                            <b>{{ auctionConfirm.user.first_name }} {{ auctionConfirm.user.last_name }}</b>
                        </a>
                    </td>
                    <td>
                        <a style="cursor: pointer; color: rgb(6 103 249);" @click="modalLot(auctionConfirm)">
                            <b>{{ auctionConfirm.lot.name }}</b>
                        </a>
                    </td>
                    <td v-if="auctionConfirm.confirmed_admin == 1">
                      Подтверждено
                    </td>
                    <td v-if="auctionConfirm.confirmed_admin == 0">
                      Не подтверждено
                    </td>

                    <td class="text-center">
                      <a @click="updateAuctionConfirm(auctionConfirm.id)">
                        <button class="col-md-5 form-control btn btn-primary">
                            Подтвердить
                        </button>
                      </a>
                      <a @click="deleteAuctionConfirm(auctionConfirm.id)">
                          <button class="col-md-5 form-control btn btn-danger">
                              Отклонить
                          </button>
                      </a>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
            <!-- /.card-body -->
            <div class="card-footer">
              <pagination
                :data="auctionConfirms"
                @pagination-change-page="getResults"
              ></pagination>
            </div>
          </div>
          <!-- /.card -->
        </div>
      </div>

        <div class="modal fade" id="user_information" tabindex="-1" role="dialog" aria-labelledby="user_information" aria-hidden="true">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" v-show="editmode">Детальная информация пользователя</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                    </button>
                </div>

                <form>
                    <div class="modal-body">

                        <div class="form-group">
                            <label>Тип пользователя</label>
                            <input v-if="form.user.type_user == 'phys'" readonly value="Физ. лицо" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <input v-else-if="form.user.type_user == 'jur'" readonly value="Юр. лицо" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <input v-else readonly value="ИП" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <has-error :form="form" field="user"></has-error>
                        </div>

                        <div class="form-group">
                            <label>Полное наименование</label>
                            <input readonly :value="form.user.full_name" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <has-error :form="form" field="user"></has-error>
                        </div>

                        <div class="form-group">
                            <label>Имя</label>
                            <input readonly :value="form.user.first_name" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <has-error :form="form" field="user"></has-error>
                        </div>

                        <div class="form-group">
                            <label>Фамилия</label>
                            <input readonly :value="form.user.last_name" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <has-error :form="form" field="user"></has-error>
                        </div>

                        <div class="form-group">
                            <label>Email</label>
                            <input readonly :value="form.user.email" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <has-error :form="form" field="user"></has-error>
                        </div>

                        <div class="form-group">
                            <label>Телефон</label>
                            <input readonly :value="form.user.phone" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <has-error :form="form" field="user"></has-error>
                        </div>

                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                    </div>
                  </form>
                </div>
            </div>
        </div>

        <div class="modal fade" id="lot_information" tabindex="-1" role="dialog" aria-labelledby="lot_information" aria-hidden="true">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" v-show="editmode">Детальная информация лота</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                    </button>
                </div>

                <form>
                    <div class="modal-body">

                        <div class="form-group">
                            <label>Название аукциона</label>
                            <input readonly :value="form.auction.name" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <has-error :form="form" field="user"></has-error>
                        </div>

                        <div class="form-group">
                            <label>Номер аукциона</label>
                            <input readonly :value="form.auction.auction_number" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <has-error :form="form" field="user"></has-error>
                        </div>

                        <div class="form-group">
                            <label>Название</label>
                            <input readonly :value="form.lot.name" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <has-error :form="form" field="user"></has-error>
                        </div>

                        <div class="form-group">
                            <label>Номер лота</label>
                            <input readonly :value="form.lot.lot_number" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <has-error :form="form" field="user"></has-error>
                        </div>

                        <div class="form-group">
                            <label>Начальная цена</label>
                            <input readonly :value="form.lot.price_start" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <has-error :form="form" field="user"></has-error>
                        </div>

                        <div class="form-group" v-if="form.auction.type == 'econom'">
                            <label>Минимальная цена</label>
                            <input readonly :value="form.lot.price_min" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <has-error :form="form" field="user"></has-error>
                        </div>

                        <div class="form-group">
                            <label>Статус</label>
                            <input readonly :value="form.lot.status" type="text" name="user"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('user') }">
                            <has-error :form="form" field="user"></has-error>
                        </div>

                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                    </div>
                  </form>
                </div>
            </div>
        </div>

    </div>
  </section>
</template>

<script>
import VueTagsInput from "@johmun/vue-tags-input";

export default {
  components: {
    VueTagsInput,
  },
  data() {
    return {
      editmode: false,
      auctionConfirms: {},
      form: new Form({
        /* MAIN */
        id: "",
        type: "",
        user_id: "",
        lot_id: "",
        confirmed_admin: false,
        deleted_at: "",
        user: "",
        lot: "",
        auction: "",

        /* ADDITIONAL */
        // user: "",
      }),
      // users: [],

      autocompleteItems: [],
    };
  },
  methods: {
    loadAuctionConfirms() {
      // if(this.$gate.isAdmin()){
      axios
        .get("/api/auctionConfirm")
        .then(({ data }) => (this.auctionConfirms = data.data));
      // }
    },
    modalUser(auctionConfirm) {
      this.editmode = true;
      this.form.reset();
      $("#user_information").modal("show");
      this.form.fill(auctionConfirm);
    },

    modalLot(auctionConfirm) {
      this.editmode = true;
      this.form.reset();
      $("#lot_information").modal("show");
      this.form.fill(auctionConfirm);
    },

    getResults(page = 1) {
      this.$Progress.start();

      axios
        .get("/api/auctionConfirm?page=" + page)
        .then(({ data }) => (this.auctionConfirms = data.data));

      this.$Progress.finish();
    },

    deleteAuctionConfirm(id) {
      Swal.fire({
        title: "Are you sure?",
        text: "You won't be able to revert this!",
        showCancelButton: true,
        confirmButtonColor: "#d33",
        cancelButtonColor: "#3085d6",
        confirmButtonText: "Yes, delete it!",
      }).then((result) => {
        // Send request to the server
        if (result.value) {
          this.form
            .delete("/api/auctionConfirm/" + id)
            .then(() => {
              Swal.fire("Deleted!", "Your file has been deleted.", "success");
              // Fire.$emit('AfterCreate');
              this.loadAuctionConfirms();
            })
            .catch((data) => {
              Swal.fire("Failed!", data.message, "warning");
            });
        }
      });
    },

    updateAuctionConfirm(id) {
      this.$Progress.start();
      this.form.put("/api/auctionConfirm/" + id);
      this.$Progress.finish();
      this.loadAuctionConfirms();
    },
  },
  mounted() {},
  created() {
    this.$Progress.start();

    this.loadAuctionConfirms();

    this.$Progress.finish();
  },
  filters: {
    truncate: function (text, length, suffix) {
      return text.substring(0, length) + suffix;
    },
  },
  computed: {
    filteredItems() {
      return this.autocompleteItems.filter((i) => {
        return i.text.toLowerCase().indexOf(this.tag.toLowerCase()) !== -1;
      });
    },
  },
};
</script>
