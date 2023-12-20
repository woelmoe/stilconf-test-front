<template></template>

<script lang="ts" setup>
import { useMainStore } from '~/store/main'
import { useContext } from '@nuxtjs/composition-api'
import { onMounted } from '@nuxtjs/composition-api'

enum NetworkPerformanceSpeed {
  low = '100kb',
  mid = '500kb',
  high = '2mb',
}
interface IUser {
  username: string
  speed: NetworkPerformanceSpeed
}

// const main = useMainStore()
const { $axios } = useContext()
async function getAllUsers() {
  return await $axios.$get('http://localhost:3001/users')
}
async function getUser(id: string) {
  return await $axios.$get(`http://localhost:3001/users/${id}`)
}

async function createUser(params: IUser) {
  return await $axios.$post('http://localhost:3001/users', params)
}

async function updateUser(id: string, params: IUser) {
  return await $axios.$post(`http://localhost:3001/users/update/${id}`, params)
}

async function deleteUser(id: string) {
  return await $axios.$get(`http://localhost:3001/users/delete/${id}`)
}

const newUser = {
  username: 'nik',
  speed: NetworkPerformanceSpeed.high,
}

const updatedUser = {
  username: 'nikolas',
  speed: NetworkPerformanceSpeed.low,
}

onMounted(async () => {
  // console.log(await createUser(newUser))
  const allUsersResponse = await getAllUsers()
  console.log(allUsersResponse)
  // const user0 = await getUser(allUsersResponse.data[0].userId)
  // console.log(user0)
  // const id = user0.data.userId
  // const editRes = await updateUser(id, updatedUser)
  // console.log(editRes)
  // deleteUser(id)
})
</script>
