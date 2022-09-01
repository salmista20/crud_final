<template>
	<!-- Modal -->
	<b-modal ref="mdlDatosProducto" hide-footer :title="titulo">
		<div class="d-block">
			<b-form>
				<b-form-group label="Producto">
					<b-form-input
						v-model="frmDatosProducto.producto"
						type="text"
						placeholder="Ingrese el producto"
						required
					></b-form-input>
				</b-form-group>
				<b-form-textarea
					v-model="frmDatosProducto.descripcion"
					placeholder="Descripción..."
					rows="3"
					max-rows="6"
				></b-form-textarea>
				<b-form-checkbox
					v-model="frmDatosProducto.habilitado"
					v-if="modo == 'EDITAR'"
					value="1"
					unchecked-value="0"
				>
					Habilitado
				</b-form-checkbox>
			</b-form>
		</div>
		<b-button
			class="mt-3"
			variant="outline-success"
			block
			@click="Guardar"
			v-if="modo == 'NUEVO'"
			>Guardar</b-button
		>
		<b-button
			class="mt-3"
			variant="outline-success"
			block
			@click="Actualizar"
			v-if="modo == 'EDITAR'"
			>Actualizar</b-button
		>

		<b-button class="mt-3" variant="outline-danger" block @click="Cerrar"
			>Cerrar</b-button
		>
	</b-modal>
</template>

<script>
import axios from "axios";
export default {
	props: {},
	data() {
		return {
			modo: null,
			frmDatosProducto: {
				id: null,
				producto: null,
				descripcion: null,
				habilitado: 0,
			},
		};
	},
	computed: {
		titulo() {
			let valor = null;

			if (this.modo == "NUEVO") {
				valor = "NUEVO PRODUCTO";
			} else if (this.modo == "EDITAR") {
				valor = "EDITAR PRODUCTO";
			}

			return valor;
		},
	},
	methods: {
		Abrir() {
			this.$refs["mdlDatosProducto"].show();
		},
		Cerrar() {
			this.$refs["mdlDatosProducto"].hide();
		},
		Guardar() {
			let self = this;
			if (this.frmDatosProducto.producto == null) {
				this.$swal.fire({
					icon: "error",
					title: "¡Ups!",
					text: "El PRODUCTO no puede quedar vacío.",
				});
				return false;
			}

			this.$swal
				.fire({
					icon: "question",
					text: "¿DESEA REGISTRAR ESTE PRODUCTO?",
					confirmButtonText: "Si",
					showCancelButton: true,
					cancelButtonText: "No",
					allowOutsideClick: false,
				})
				.then(async (result) => {
					if (result.isConfirmed) {
						let data = new FormData();
						data.append("producto", self.frmDatosProducto.producto);
						data.append("descripcion", self.frmDatosProducto.descripcion);

						await axios
							.post("http://laravel.test/api/productos", data)
							.then(function (response) {
								let status = response.status;
								if (status == 200) {
									self.$parent.ListarProductos();
									self.Cerrar();
									return self.$swal.fire({
										icon: "success",
										title: "¡ÉXITO!",
										text: "El registro se agregó correctamente.",
									});
								}
							});
					}
				});
		},
		Actualizar() {
			let self = this;
			if (this.frmDatosProducto.producto == null) {
				this.$swal.fire({
					icon: "error",
					title: "¡Ups!",
					text: "El PRODUCTO no puede quedar vacío.",
				});
				return false;
			}

			this.$swal
				.fire({
					icon: "question",
					text: "¿DESEA GUARDAR LOS CAMBIOS?",
					confirmButtonText: "Si",
					showCancelButton: true,
					cancelButtonText: "No",
					allowOutsideClick: false,
				})
				.then((result) => {
					if (result.isConfirmed) {
						let producto_id = self.frmDatosProducto.id;

						let data = {
							producto: self.frmDatosProducto.producto,
							descripcion: self.frmDatosProducto.descripcion,
							habilitado: self.frmDatosProducto.habilitado,
						};

						axios
							.put("http://laravel.test/api/productos/" + producto_id, data)
							.then(function (response) {
								let status = response.status;
								if (status == 200) {
									self.$parent.ListarProductos();
									self.Cerrar();
									return self.$swal.fire({
										icon: "success",
										title: "¡ÉXITO!",
										text: "El registro se actualizó correctamente.",
									});
								}
							});
					}
				});
		},
	},
};
</script>

<style class="css">
/* #mdlDatosProducto {
	top: 20%;
} */
</style>