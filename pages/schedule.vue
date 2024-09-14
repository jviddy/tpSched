<template>
    <div class="container mx-auto p-4">
        <h1 class="text-2xl font-bold mb-4">Holiday Schedule Planner</h1>
        
        <div class="mb-4 flex space-x-4">
            <div>
                <label for="numDays" class="block mb-1">Number of Days:</label>
                <select id="numDays" v-model="numDays" class="border p-2 rounded">
                    <option v-for="n in 14" :key="n" :value="n">{{ n }}</option>
                </select>
            </div>
            
            <div>
                <label for="startDay" class="block mb-1">Start Day:</label>
                <select id="startDay" v-model="startDay" class="border p-2 rounded">
                    <option v-for="day in weekDays" :key="day" :value="day">{{ day }}</option>
                </select>
            </div>
        </div>

        <div class="space-y-4">
            <div v-for="(day, index) in scheduleDays" :key="index" class="border p-4 rounded shadow">
                <h2 class="text-xl font-semibold mb-2">{{ day.name }} (Day {{ index + 1 }})</h2>
                <div v-if="day.activities.length === 0" @click="addActivity(day)" class="cursor-pointer text-blue-500 hover:underline">
                    + Add activity
                </div>
                <div v-else class="space-y-2">
                    <div v-for="(activity, actIndex) in day.activities" :key="actIndex" class="bg-gray-100 p-2 rounded">
                        <div v-if="activity.editing">
                            <input
                                v-model="activity.name"
                                @input="suggestActivities(activity)"
                                @keyup.enter="finishEditing(activity)"
                                class="w-full p-1 border rounded"
                                placeholder="Enter activity name"
                            >
                            <div v-if="activity.suggestions.length > 0" class="mt-1 bg-white border rounded shadow">
                                <div
                                    v-for="suggestion in activity.suggestions"
                                    :key="suggestion"
                                    @click="selectSuggestion(activity, suggestion)"
                                    class="p-1 hover:bg-gray-200 cursor-pointer"
                                >
                                    {{ suggestion }}
                                </div>
                            </div>
                        </div>
                        <div v-else>
                            {{ activity.name }}
                        </div>
                    </div>
                    <div @click="addActivity(day)" class="cursor-pointer text-blue-500 hover:underline">
                        + Add another activity
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, watch } from 'vue'

const numDays = ref(7)
const startDay = ref('Monday')
const weekDays = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']

const commonActivities = [
    'Visit Disney World', 'Universal Studios Tour', 'SeaWorld Adventure', 'Kennedy Space Center',
    'Gatorland', 'ICON Park', 'Orlando Science Center', 'Madame Tussauds', 'The Wheel at ICON Park',
    'Legoland Florida', 'Crayola Experience', 'Ripley\'s Believe It or Not!', 'WonderWorks',
    'Boggy Creek Airboat Rides', 'Orlando Museum of Art', 'Lake Eola Park', 'Shopping at Mall at Millenia'
]

const scheduleDays = ref([])

const initializeScheduleDays = () => {
    scheduleDays.value = Array.from({ length: numDays.value }, (_, i) => ({
        name: weekDays[(weekDays.indexOf(startDay.value) + i) % 7],
        activities: []
    }))
}

initializeScheduleDays()

const addActivity = (day) => {
    day.activities.push({ name: '', editing: true, suggestions: [] })
}

const suggestActivities = (activity) => {
    activity.suggestions = commonActivities.filter(a => 
        a.toLowerCase().includes(activity.name.toLowerCase())
    ).slice(0, 5)
}

const selectSuggestion = (activity, suggestion) => {
    activity.name = suggestion
    finishEditing(activity)
}

const finishEditing = (activity) => {
    activity.editing = false
    activity.suggestions = []
}

// Watch for changes in numDays and startDay
watch([numDays, startDay], initializeScheduleDays)
</script>

<style scoped>
/* Component-specific styles can be added here if needed */
</style>