<script>
export default {
	data: function() {
		return {
			errormsg: null,
			loading: false,
			fountains: [],
		}
	},
	methods: {
		load() {
			return load
		},
		async refresh() {
			this.loading = true;
			this.errormsg = null;
			try {
				let response = await this.$axios.get("/fountains/");
				this.fountains = response.data;
			} catch (e) {
				this.errormsg = e.toString();
			}
			this.loading = false;
		},
		async newItem() {
			this.$router.push("/new");
		},
		async deleteFountain(id) {
			this.loading = true;
			this.errormsg = null;
			try {
				await this.$axios.delete("/fountains/" + id);

				await this.refresh();
			} catch (e) {
				this.errormsg = e.toString();
			}
			this.loading = false;
		},
		async duplicateFountain(status, longitude, latitude) {
			this.loading = true;
			this.errormsg = null;
			try {
				await this.$axios.post("/fountains/", {
					status: status,
					latitude: latitude,
					longitude: longitude,
				});

				await this.refresh();
			} catch (e) {
				this.errormsg = e.toString();
			}
			this.loading = false;
		}
	},
	mounted() {
		this.refresh()
	}
}
</script>

<template>
	<div>
		<div
			class="d-flex justify-content-between flex-wrap flex-md-nowrap align-items-center pt-3 pb-2 mb-3 border-bottom">
			<h1 class="h2">Administrative Fountains list</h1>
			<div class="btn-toolbar mb-2 mb-md-0">
				<div class="btn-group me-2">
					<button type="button" class="btn btn-sm btn-outline-secondary" @click="refresh">
						Refresh
					</button>
				</div>
				<div class="btn-group me-2">
					<button type="button" class="btn btn-sm btn-outline-primary" @click="newItem">
						New
					</button>
				</div>
			</div>
		</div>

		<ErrorMsg v-if="errormsg" :msg="errormsg"></ErrorMsg>

		<LoadingSpinner v-if="loading"></LoadingSpinner>

		<div class="card" v-if="fountains.length === 0">
			<div class="card-body">
				<p>No fountains yet.</p>

				<a href="javascript:" class="btn btn-primary" @click="newItem">Create a new fountain</a>
			</div>
		</div>

		<div class="card" v-if="!loading" v-for="f in fountains">
			<div class="card-header">
				Fountain {{ f.id }}
				<span v-if="f.status=='good'" class="badge text-bg-success">{{f.status}}</span>
				<span v-if="f.status=='faulty'" class="badge text-bg-danger">{{f.status}}</span>
			</div>
			<div class="card-body">
				<p class="card-text">
					Fountain {{ f.id }}.
					<br />The status is {{ f.status }}.
					<br />Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
					<br />Coordinates:
					<ul>
						<li>Longitude: {{ f.longitude }}</li>
						<li>Latitude: {{ f.latitude }}</li>
					</ul>
				</p>
				<div class="d-grid gap-2 d-md-flex justify-content-md-end">
					<a href="javascript:" class="btn btn-danger" @click="deleteFountain(f.id)">Delete</a>
					<a href="javascript:" class="btn btn-info" @click="duplicateFountain(f.status, f.longitude, f.latitude)">Duplicate</a>
				</div>
			</div>
		</div>
	</div>
</template>

<style scoped>
.card {
	margin-bottom: 20px;
}
</style>
