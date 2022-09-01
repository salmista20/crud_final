<template>
	<div>
		<h1 class="text-center">LISTA DE PRODUCTOS</h1>

		<div class="text-center">
			<b-button variant="primary" @click="Nuevo">Agregar</b-button>
		</div>
		<br />
		<div class="col-md-8 offset-md-2">
			<b-table
				:sticky-header="true"
				:no-border-collapse="false"
				responsive
				:items="lista_productos"
				:fields="columnas"
			>
				<!-- We are using utility class `text-nowrap` to help illustrate horizontal scrolling -->

				<template #head()="scope">
					<div class="text-nowrap">{{ scope.label }}</div>
				</template>

				<template #cell(N°)="data">
					{{ data.index + 1 }}
				</template>
				<template #cell(habilitado)="data">
					{{ data.item.habilitado ? "SI" : "NO" }}
				</template>

				<template #cell(acciones)="{ item }">
					<b-button-group>
						<b-btn variant="warning" @click="Editar(item)">Editar</b-btn>
						<b-btn variant="danger" @click="Eliminar(item)">Eliminar</b-btn>
					</b-button-group>
				</template>
			</b-table>
		</div>

		<mdlDatosProducto ref="mdlDatosProducto"></mdlDatosProducto>
	</div>
</template>

<script>
import axios from "axios";
import mdlDatosProducto from "./components/mdlDatosProducto.vue";
export default {
	components: { mdlDatosProducto },
	props: [],
	data() {
		return {
			lista_productos: [],
			columnas: [
				"N°",
				{ key: "producto", label: "Producto" },
				{ key: "descripcion", label: "Descripción" },
				{ key: "habilitado", label: "Habilitado" },
				{ key: "created_at", label: "Creado" },
				{ key: "updated_at", label: "Actualizado" },
				"acciones",
			],
		};
	},
	computed: {},
	mounted() {
		this.ListarProductos();
	},
	methods: {
		async ListarProductos() {
			let self = this;
			await axios
				.get("http://laravel.test/api/productos")
				.then(function (response) {
					self.lista_productos = response.data.data;
				});
		},

		Nuevo() {
			let mdlDatosProducto = this.$refs.mdlDatosProducto;
			mdlDatosProducto.frmDatosProducto.producto = null;
			mdlDatosProducto.frmDatosProducto.descripcion = null;
			mdlDatosProducto.frmDatosProducto.habilitado = 1;
			mdlDatosProducto.modo = "NUEVO";
			mdlDatosProducto.Abrir();
		},
		Editar(item) {
			let mdlDatosProducto = this.$refs.mdlDatosProducto;
			mdlDatosProducto.frmDatosProducto.id = item.id;
			mdlDatosProducto.frmDatosProducto.producto = item.producto;
			mdlDatosProducto.frmDatosProducto.descripcion = item.descripcion;
			mdlDatosProducto.frmDatosProducto.habilitado = item.habilitado;
			mdlDatosProducto.modo = "EDITAR";
			mdlDatosProducto.Abrir();
		},
		Eliminar(item) {
			let self = this;

			this.$swal
				.fire({
					icon: "question",
					text: "¿DESEA ELIMINAR ESTE PRODUCTO?",
					confirmButtonText: "Si",
					showCancelButton: true,
					cancelButtonText: "No",
					allowOutsideClick: false,
				})
				.then((result) => {
					if (result.isConfirmed) {
						let producto_id = item.id;

						axios
							.delete("http://laravel.test/api/productos/" + producto_id)
							.then(function (response) {
								let status = response.status;
								if (status == 200) {
									self.ListarProductos();
									return self.$swal.fire({
										icon: "success",
										title: "¡ÉXITO!",
										text: "El registro se eliminó correctamente.",
									});
								}
							});
					}
				});
		},
	},
};
</script>

<style>
</style>