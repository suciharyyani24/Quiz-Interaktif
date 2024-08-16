<template>
  <div class="quiz-container mx-auto mt-10 max-w-lg p-4">
    <h1 class="text-5xl font-extrabold text-center mb-8 text-blue-600">Kuis Interaktif</h1>

    <!-- Progress Bar -->
    <div class="w-full bg-gray-200 rounded-full h-2.5 mb-4">
      <div
        class="bg-blue-600 h-2.5 rounded-full"
        :style="{ width: `${progress}%` }"
      ></div>
    </div>

    <!-- Timer -->
    <div v-if="!quizCompleted">
      <div class="bg-white p-8 rounded-lg shadow-xl">
        <h2 class="text-3xl font-semibold mb-6 text-gray-800">{{ currentQuestion.question }}</h2>

        <div class="text-xl text-center mb-4">Waktu tersisa: {{ timeLeft }} detik</div>

        <div class="grid grid-cols-1 gap-4">
          <button
            v-for="(option, index) in currentQuestion.options"
            :key="index"
            @click="selectAnswer(index)"
            :class="{
              'bg-blue-500 text-white': selectedAnswer === index,
              'bg-gray-200 text-gray-700': selectedAnswer !== index
            }"
            class="py-3 px-5 rounded-lg transition-all duration-200 ease-in-out hover:bg-blue-400"
          >
            {{ option }}
          </button>
        </div>

        <div v-if="feedbackMessage" class="mt-4 text-xl text-center font-bold" :class="feedbackColor">
          {{ feedbackMessage }}
        </div>

        <button
          v-if="selectedAnswer !== null"
          @click="submitAnswer"
          class="mt-6 w-full bg-green-500 text-white py-3 px-5 rounded-lg font-semibold transition-all duration-200 ease-in-out hover:bg-green-400"
        >
          Submit Jawaban
        </button>
      </div>
    </div>

    <div v-else>
      <div class="bg-white p-8 rounded-lg shadow-xl text-center">
        <h2 class="text-4xl font-bold text-blue-600 mb-6">Kuis Selesai!</h2>
        <p class="text-2xl text-gray-800 font-semibold">Skor Anda: {{ score }} dari {{ questions.length }}</p>
        <button
          @click="restartQuiz"
          class="mt-8 w-full bg-blue-500 text-white py-3 px-5 rounded-lg font-semibold transition-all duration-200 ease-in-out hover:bg-blue-400"
        >
          Coba Lagi
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      currentQuestionIndex: 0,
      selectedAnswer: null,
      score: 0,
      quizCompleted: false,
      feedbackMessage: '',
      feedbackColor: '',
      timeLeft: 15, // 15 detik untuk setiap pertanyaan
      timer: null,
      questions: [
        {
          question: 'Apa ibu kota Indonesia?',
          options: ['Jakarta', 'Bandung', 'Surabaya', 'Medan'],
          correctAnswer: 0,
        },
        {
          question: 'Berapa jumlah planet di tata surya?',
          options: ['7', '8', '9', '10'],
          correctAnswer: 1,
        },
        {
          question: 'Siapa penemu lampu pijar?',
          options: ['Albert Einstein', 'Isaac Newton', 'Thomas Edison', 'Nikola Tesla'],
          correctAnswer: 2,
        },
      ],
    };
  },
  computed: {
    currentQuestion() {
      return this.questions[this.currentQuestionIndex];
    },
    progress() {
      return ((this.currentQuestionIndex + 1) / this.questions.length) * 100;
    },
  },
  methods: {
    startTimer() {
      this.timer = setInterval(() => {
        if (this.timeLeft > 0) {
          this.timeLeft--;
        } else {
          this.submitAnswer(); // Submit jawaban otomatis jika waktu habis
        }
      }, 1000);
    },
    resetTimer() {
      clearInterval(this.timer);
      this.timeLeft = 15; // Reset ke 15 detik
    },
    selectAnswer(index) {
      this.selectedAnswer = index;
      this.feedbackMessage = '';
    },
    submitAnswer() {
      this.resetTimer();

      if (this.selectedAnswer === this.currentQuestion.correctAnswer) {
        this.score++;
        this.feedbackMessage = 'Jawaban Benar!';
        this.feedbackColor = 'text-green-500';
      } else {
        this.feedbackMessage = 'Jawaban Salah!';
        this.feedbackColor = 'text-red-500';
      }

      setTimeout(() => {
        this.selectedAnswer = null;
        this.feedbackMessage = '';
        if (this.currentQuestionIndex < this.questions.length - 1) {
          this.currentQuestionIndex++;
          this.startTimer(); // Mulai timer lagi
        } else {
          this.quizCompleted = true;
        }
      }, 1000);
    },
    restartQuiz() {
      this.currentQuestionIndex = 0;
      this.score = 0;
      this.quizCompleted = false;
      this.startTimer();
    },
  },
  mounted() {
    this.startTimer();
  },
  beforeDestroy() {
    clearInterval(this.timer); // Bersihkan interval ketika komponen dihapus
  },
};
</script>
