<template>
    <div class="main-content">
        <article class="blog active" data-page="blog">
            <header>
                <h2 class="h2 article-title">Facebook Video Downloader</h2>
            </header>
            
            <section class="about-text">
                <div class="about-text__content">
                    <p class="h4">Download Facebook videos and reels in HD and SD quality.</p>
                    <p class="h4">Just paste the video URL below and click the download button.</p>
                    <p class="h4">Note: This tool is for educational purposes only. Please respect copyright laws.</p>
                    <p class="h4">If you have any questions or feedback, feel free to reach out.</p>
                    <p class="h4">Happy downloading!</p>
                </div>
                <form @submit.prevent="submitForm" class="contact-form" data-form>
                    <div class="input-wrapper w-full">
                        <input
                        v-model="videoUrl"
                        type="url"
                        name="videoUrl"
                        required
                        placeholder="https://www.facebook.com/video-url"
                        class="form-input w-full"
                        data-form-input
                        />
                    </div>
                    <button class="form-btn mt-4" type="submit" :disabled="loading || !videoUrl">
                        <span v-if="!loading">Download Video</span>
                        <span v-else>Loading...</span>
                    </button>
                        
                </form>
                <div v-if="message" class="h4 mt-4" :style="{ color: isError ? '#f66' : '#fc0' }">
                    {{ message }}
                </div>
                
                <div v-if="!isError && videoTitle" class="mt-4">
                    <div class="mt-4 grid grid-cols-1 sm:grid-cols-2 gap-4">
                        <a v-if="sdLink" :href="sdLink" class="form-btn text-center" download>Download Low Quality</a>
                        <a v-if="hdLink" :href="hdLink" class="form-btn text-center" download>Download High Quality</a>
                        
                    </div>
                </div>
                
                <div v-if="!isError && videoTitle" class="mt-4">
                    <h3 class="h4">{{ videoTitle }}</h3>
                    <img :src="thumbnail" alt="Thumbnail" class="rounded-lg shadow mb-3" style="max-width: 100%" />
                    
                </div>
                
            </section>
            
        </article>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const videoUrl = ref('')
const hdLink = ref('')
const sdLink = ref('')
const videoTitle = ref('')
const thumbnail = ref('')
const loading = ref(false)
const message = ref('')
const isError = ref(false)

const submitForm = async () => {
    loading.value = true
    message.value = ''
    hdLink.value = ''
    sdLink.value = ''
    videoTitle.value = ''
    thumbnail.value = ''

    try {
        const res = await fetch(
            'https://facebook-reel-and-video-downloader.p.rapidapi.com/app/main.php?url=' +
            encodeURIComponent(videoUrl.value),
            {
            method: 'GET',
            headers: {
                'X-RapidAPI-Key': '2b1800a217msh88f1bf5cfcca0b4p1c4cc4jsncb24d9c4b67c',
                'X-RapidAPI-Host': 'facebook-reel-and-video-downloader.p.rapidapi.com',
            },
            }
        )
        const data = await res.json()

        if (res.ok && data.success && data.media?.[0]) {
            const media = data.media[0]
            hdLink.value = media.hd_url
            sdLink.value = media.sd_url
            videoTitle.value = data.title
            thumbnail.value = data.thumbnail
            message.value = 'Video found!'
            isError.value = false
        } else {
            throw new Error('Video not found or private.')
        }
    } catch (err: any) {
        message.value = err.message || 'Error'
        isError.value = true
    } finally {
        loading.value = false
    }
}
</script>