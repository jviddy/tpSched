<template>
    <!-- Main container for the schedule planner -->
    <div class="container mx-auto p-4">
        <!-- Title of the schedule planner -->
        <h1 class="text-2xl font-bold mb-4">Holiday Schedule Planner</h1>

        <!-- Section for selecting number of days and start day -->
        <div class="mb-4 flex space-x-4">
            <div>
                <!-- Label and dropdown for selecting number of days -->
                <label for="numDays" class="block mb-1">Number of Days:</label>
                <select id="numDays" v-model="numDays" class="border p-2 rounded">
                    <option v-for="n in 21" :key="n" :value="n">{{ n }}</option>
                </select>
            </div>

            <div>
                <label for="startDay" class="block mb-1">Start Day:</label>
                <select id="startDay" v-model="startDay" class="border p-2 rounded">
                    <option v-for="day in weekDays" :key="day" :value="day">{{ day }}</option>
                </select>
            </div>
        </div>

        <draggable 
            v-model="scheduleDays" 
            class="space-y-4" 
            item-key="name" 
            @start="drag=true" 
            @end="onDragEnd"
        >
            <template #item="{element, index}">
                <div 
                    class="border p-4 rounded shadow" 
                    :key="element.name"
                    :class="{ 'cursor-move': drag }"
                >
                    <h2 class="text-xl font-semibold mb-2 hover:cursor-move" :title="element.name">
                        {{ element.name }} (Day {{ index + 1 }})
                    </h2>

                    <draggable
                        v-model="element.activities"
                        group="activities"
                        animation="200"
                        ghost-class="ghost"
                        @start="drag=true"
                        @end="drag=false"
                    >
                        <template #item="{element: activity, index: actIndex}">
                            <div class="space-y-2 bg-gray-100 p-2 rounded mb-2 cursor-move">
                                <div v-if="activity.editing">
                                    <input
                                        v-model="activity.name"
                                        @input="suggestActivities(activity)"
                                        @keydown="handleKeyDown($event, activity)"
                                        @keyup.enter="finishEditing(activity)"
                                        class="w-full p-1 border rounded"
                                        placeholder="Enter activity name"
                                    >
                                    <div v-if="activity.suggestions.length > 0" class="mt-1 bg-white border rounded shadow max-h-32 overflow-y-auto">
                                        <div
                                            v-for="(suggestion, suggestionIndex) in activity.suggestions"
                                            :key="suggestion"
                                            @click="selectSuggestion(activity, suggestion)"
                                            :class="{'bg-blue-500 text-white': suggestionIndex === activity.selectedSuggestionIndex, 'p-1 hover:bg-gray-200 cursor-pointer': true}"
                                        >
                                            {{ suggestion }}
                                        </div>
                                    </div>
                                </div>
                                <div v-else>
                                    <textarea
                                        v-text="activity.name"
                                        @click="activity.editing = true"
                                        class="w-full p-1 border rounded"
                                        rows="1"
                                    ></textarea>
                                    <button class="text-blue-500 hover:underline" @click="addActivityBefore(element, actIndex)">+ Add activity before</button>
                                </div>
                            </div>
                        </template>
                    </draggable>

                    <div v-if="element.activities.length === 0" @click="addActivity(element)" class="cursor-pointer text-blue-500 hover:underline">
                        + Add activity
                    </div>
                    <div v-else @click="addActivity(element)" class="cursor-pointer text-blue-500 hover:underline">
                        + Add another activity
                    </div>
                </div>
            </template>
        </draggable>
    </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue';
import draggable from 'vuedraggable';

const numDays = ref(7);
const startDay = ref('Monday');
const weekDays = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday'];

const commonActivities = [
    'Visit Disney World', 'Universal Studios Tour', 'SeaWorld Adventure', 'Kennedy Space Center',
    'Gatorland', 'ICON Park', 'Orlando Science Center', 'Madame Tussauds', 'The Wheel at ICON Park',
    'Legoland Florida', 'Crayola Experience', 'Ripley\'s Believe It or Not!', 'WonderWorks',
    'Boggy Creek Airboat Rides', 'Orlando Museum of Art', 'Lake Eola Park', 'Shopping at Mall at Millenia'
];

const scheduleDays = ref([]);
const drag = ref(false);

const initializeScheduleDays = () => {
    scheduleDays.value = Array.from({ length: numDays.value }, (_, i) => ({
        name: weekDays[(weekDays.indexOf(startDay.value) + i) % 7],
        activities: []
    }));
};

// Initialize days on component mount
onMounted(() => {
    initializeScheduleDays();
});

const updateDayNames = () => {
    scheduleDays.value = scheduleDays.value.map((day, index) => ({
        ...day,
        name: weekDays[(weekDays.indexOf(startDay.value) + index) % 7]
    }));
};

const addActivity = (day) => {
    day.activities.push({ name: '', editing: true, suggestions: [], selectedSuggestionIndex: 0 });
};

const addActivityBefore = (day, actIndex) => {
    day.activities.splice(actIndex, 0, { name: '', editing: true, suggestions: [], selectedSuggestionIndex: 0 });
};

const suggestActivities = (activity) => {
    activity.suggestions = commonActivities.filter(a =>
        a.toLowerCase().includes(activity.name.toLowerCase())
    ).slice(0, 5);
    activity.selectedSuggestionIndex = 0;
};

const selectSuggestion = (activity, suggestion) => {
    activity.name = suggestion;
    finishEditing(activity);
};

const finishEditing = (activity) => {
    activity.editing = false;
    activity.suggestions = [];
    activity.selectedSuggestionIndex = -1;
};

const handleKeyDown = (event, activity) => {
    if (activity.suggestions.length > 0) {
        if (event.key === 'ArrowDown') {
            event.preventDefault();
            activity.selectedSuggestionIndex = (activity.selectedSuggestionIndex + 1) % activity.suggestions.length;
        } else if (event.key === 'ArrowUp') {
            event.preventDefault();
            activity.selectedSuggestionIndex = (activity.selectedSuggestionIndex - 1 + activity.suggestions.length) % activity.suggestions.length;
        } else if (event.key === 'Enter') {
            event.preventDefault();
            if (activity.suggestions.length > 0) {
                selectSuggestion(activity, activity.suggestions[activity.selectedSuggestionIndex]);
            } else {
                finishEditing(activity);
            }
        }
    }
};

const onDragEnd = () => {
    drag.value = false;
    updateDayNames();
};

const onDragStart = () => {
    drag.value = true;
};

// Watch for changes in numDays and startDay
watch([numDays, startDay], initializeScheduleDays);
</script>

<style scoped>
/* Add custom component-specific styles here */
.cursor-move {
    cursor: move;
}
.suggestion.active {
    background-color: #007bff;
    color: white;
}
.ghost {
    opacity: 0.5;
}
</style>
