<template>
  <div>
    <b-row class="mt-4">
      <b-col>
        <h1 class="text-center centered small"> {{ $t('task-summary-builder-rock') }}</h1>
      </b-col>
    </b-row>

    <b-row class="mt-4">
      <b-col md="4" offset-md="2">

        <ul class="list-unstyled">

          <!-- Material -->
          <b-media tag="li" vertical-align="top" class="mb-4">
            <template v-slot:aside>
              <i v-if="task.material === materials.image" class="fas fa-images icon-secondary-big"></i>
              <i v-if="task.material === materials.sound" class="fas fa-music icon-secondary-big"></i>
              <i v-if="task.material === materials.video" class="fas fa-play icon-secondary-big"></i>
              <i v-if="task.material === materials.pdf" class="fas fa-file-pdf icon-secondary-big"></i>
              <i v-if="task.material === materials.tweet" class="fab fa-twitter icon-secondary-big"></i>
            </template>
              <h1 class="small text-muted m-0 pb-1">{{ $t('task-summary-builder-material') }}</h1>
              <legend class="mt-0 mb-0">{{ task.material.toUpperCase() }}</legend>
          </b-media>

          <!-- Job -->
          <b-media tag="li" vertical-align="top" class="mb-4">
            <template v-slot:aside>
              <i v-if="task.job === jobs.describe" class="fas fa-edit icon-secondary-big"></i>
              <i v-if="task.job === jobs.classify" class="fas fa-filter icon-secondary-big"></i>
              <i v-if="task.job === jobs.count" class="fas fa-calculator icon-secondary-big"></i>
            </template>
              <h1 class="small text-muted m-0 pb-1">{{ $t('task-summary-builder-job') }}</h1>
              <legend class="mt-0 mb-0">{{ task.job.toUpperCase() }}</legend>
          </b-media>
        </ul>
      </b-col>

      <!-- Source section -->
      <b-col md="5" offset-md="1">
        <ul class="list-unstyled">
          <b-media tag="li" vertical-align="top">
            <template v-slot:aside>
              <i v-if="task.source === sources.amazon" class="fab fa-aws icon-secondary-big"></i>
              <i v-if="task.source === sources.dropbox" class="fab fa-dropbox icon-secondary-big"></i>
              <i v-if="task.source === sources.flickr" class="fab fa-flickr icon-secondary-big"></i>
              <i v-if="task.source === sources.twitter" class="fab fa-twitter icon-secondary-big"></i>
            </template>
              <h1 class="small text-muted mt-0 mb-0 pb-1">{{ $t('task-summary-builder-importer') }}</h1>
              <legend class="m-0">{{ task.source.toUpperCase() }}</legend>
            <b-media tag="li" vertical-align="top">
              <template v-slot:aside>
                <i class="fas fa-tasks icon-secondary-big"></i>
              </template>
              <span v-if="task.source !== sources.flickr && task.source !== sources.twitter"><b>{{ task.sourceContent.length }}</b> {{ $t('task-summary-builder-tasks') }}</span>
              <ul v-if="task.source !== sources.flickr && task.source !== sources.twitter">
                <li :key="key" v-for="(file, key) in task.sourceContent">
                  <b-link v-if="task.source === sources.amazon" :href="getBucketFileLink(file)" target="_blank">{{ file }}</b-link>
                  <b-link v-else-if="task.source === sources.dropbox" :href="file.link" target="_blank">{{ file.name }}</b-link>
                  <b-link v-else :href="file" target="_blank">{{ file }}</b-link>
                </li>
              </ul>
              <p v-else-if="task.source === sources.flickr"> {{ $t('task-summary-builder-flickr-import') }} (<span class="font-italic">{{ task.sourceContent }}</span>)</p>
              <p v-else-if="task.source === sources.twitter">{{ task.sourceContent.maxTweets + '' }} {{ $t('task-summary-builder-tweets-import') }}</p>
            </b-media>
          </b-media>
        </ul>
      </b-col>
    </b-row>
    <b-row>
      <b-col md="8" offset-md="2" class="container greyish jumbotron">
        <ul class="list-unstyled">
        <!-- Template -->
        <b-media tag="li" vertical-align="top">
          <template v-slot:aside>
            <i class="fas fa-code icon-secondary-big"></i>
          </template>
          <h1 class="small text-muted m-0 pb-1 mb-2">{{ $t('task-summary-builder-template') }}</h1>
          <!-- Describe template -->
          <ul v-if="task.job === jobs.describe" class="list-unstyled">
            <li>
              <b-button v-b-toggle.collapse-1 variant="outline-secondary">{{ $t('task-summary-builder-question') }}</b-button>
              <b-collapse id="collapse-1" class="mt-2">
                <p class="mb-0">{{ task.template.question }}</p>
                <ul class="list-unstyled ml-4">
                  <li :key="index" v-for="(description, index) in task.template.descriptions">
                    <label>{{ $t('description')  + ' ' + (index + 1) }}</label>
                    <p class="mb-0">{{ description }}</p>
                  </li>
                </ul>
              </b-collapse>
            </li>
          </ul>
          <!-- Classify template -->
          <ul v-if="task.job === jobs.classify" class="list-unstyled">
            <li :key="q" v-for="(question, q) in task.template" class="mb-2">
              <b-button v-b-toggle="'collapse-' + (q + 1)" variant="outline-secondary">{{  $t('task-summary-builder-question')+' ' + (q + 1) }}</b-button>
              <b-collapse :id="'collapse-' + (q + 1)" :visible="q === 0 ? 'visible' : false" class="mt-2">
                <p class="mb-0">{{ question.question }}</p>
                <ul class="list-unstyled ml-4">
                  <li :key="a" v-for="(answer, a) in task.template[q].answers">
                    <label>{{ $t('task-summary-builder-answer')  + ' ' + (a + 1) }}</label>
                    <p class="mb-0">{{ answer }}</p>
                  </li>
                </ul>
                <hr>
              </b-collapse>
            </li>
          </ul>
          <!-- Count template -->
          <ul v-if="task.job === jobs.count" class="list-unstyled">
            <li>
              <b-button v-b-toggle.collapse-3 variant="outline-secondary">{{ $t('task-summary-builder-question') }}</b-button>
              <b-collapse id="collapse-3" class="mt-2">
                <p>{{ task.template }}</p>
              </b-collapse>
            </li>
          </ul>
        </b-media>
        </ul>
      </b-col>
    </b-row>
    <b-row>
      <b-col class="text-center">
        <b-btn
          @click="onSubmit"
          v-b-tooltip.hover
          :title="$t('task-summary-builder-onsubmit')"
          variant="primary"
          size="lg">
          {{ $t('create-btn') }}
        </b-btn>
      </b-col>
    </b-row>
  </div>
</template>

<script>
import { mapState, mapActions, mapMutations, mapGetters } from 'vuex'

import { buildTemplateFromModel } from '@/helper'

import ImageCountTemplate from '@/components/Task/Template/Image/ImageCountTemplate'
import ImageDescribeTemplate from '@/components/Task/Template/Image/ImageDescriptionTemplate'
import ImageClassificationTemplate from '@/components/Task/Template/Image/ImageClassificationTemplate'
import VideoClassificationTemplate from '@/components/Task/Template/Video/VideoClassificationTemplate'
import VideoDescriptionTemplate from '@/components/Task/Template/Video/VideoDescriptionTemplate'
import SoundClassificationTemplate from '@/components/Task/Template/Sound/SoundClassificationTemplate'
import SoundDescriptionTemplate from '@/components/Task/Template/Sound/SoundDescriptionTemplate'
import PdfDescriptionTemplate from '@/components/Task/Template/Document/PdfDescriptionTemplate'
import TwitterClassificationTemplate from '@/components/Task/Template/Twitter/TwitterClassificationTemplate'
import TwitterDescriptionTemplate from '@/components/Task/Template/Twitter/TwitterDescriptionTemplate'

export default {
  name: 'SummaryBuilder',
  computed: {
    ...mapState('task/builder', [
      'task', 'materials', 'jobs', 'sources', 'bucket'
    ]),
    ...mapState('project', [
      'selectedProject'
    ]),
    ...mapGetters('task/builder', [
      'getBucketFileLink'
    ])
  },
  methods: {
    ...mapActions('task', [
      'saveTaskPresenter','saveTaskCategory'
    ]),
    ...mapActions('task/importer', [
      'importAmazonS3Tasks',
      'importDropboxTasks',
      'importFlickrTasks',
      'importTwitterTasks'
    ]),
    ...mapActions('task/builder', {
      resetTaskBuilder: 'reset'
    }),
    ...mapMutations('notification', [
      'showSuccess', 'showError'
    ]),

    onSubmit () {
      /// -----------------------------------------------------------
      /// Template generation depending on what the user has selected
      /// -----------------------------------------------------------

      // the generated template
      let template = null

      // Image template generation
      if (this.task.material === this.materials.image) {

        if (this.task.job === this.jobs.count) {
          template = buildTemplateFromModel(ImageCountTemplate, {
            question: this.task.template
          })
        } else if (this.task.job === this.jobs.describe) {
          template = buildTemplateFromModel(ImageDescribeTemplate, {
            question: this.task.template.question,
            descriptions: this.task.template.descriptions
          })
        } else if (this.task.job === this.jobs.classify) {
          template = buildTemplateFromModel(ImageClassificationTemplate, {
            questions: this.task.template
          })
        }

      }

      // Sound template generation
      if (this.task.material === this.materials.sound) {

        if (this.task.job === this.jobs.classify) {
          template = buildTemplateFromModel(SoundClassificationTemplate, {
            questions: this.task.template
          })
        } else if (this.task.job === this.jobs.describe) {
          template = buildTemplateFromModel(SoundDescriptionTemplate, {
            question: this.task.template.question,
            descriptions: this.task.template.descriptions
          })
        }

      }

      // Video template generation
      if (this.task.material === this.materials.video) {

        if (this.task.job === this.jobs.classify) {
          template = buildTemplateFromModel(VideoClassificationTemplate, {
            questions: this.task.template
          })
        } else if (this.task.job === this.jobs.describe) {
          template = buildTemplateFromModel(VideoDescriptionTemplate, {
            question: this.task.template.question,
            descriptions: this.task.template.descriptions
          })
        }

      }

      // Pdf template generation
      if (this.task.material === this.materials.pdf) {

        if (this.task.job === this.jobs.describe) {
          template = buildTemplateFromModel(PdfDescriptionTemplate, {
            question: this.task.template.question,
            descriptions: this.task.template.descriptions
          })
        }

      }

      // Tweet template generation
      if (this.task.material === this.materials.tweet) {

        if (this.task.job === this.jobs.classify) {
          template = buildTemplateFromModel(TwitterClassificationTemplate, {
            questions: this.task.template
          })
        } else if (this.task.job === this.jobs.describe) {
          template = buildTemplateFromModel(TwitterDescriptionTemplate, {
            question: this.task.template.question,
            descriptions: this.task.template.descriptions
          })
        }

      }

      // store the generated template for the selected project
      const templatePromise = this.saveTaskPresenter({
        project: this.selectedProject,
        template: template
      })

      // store the generated template for the selected project
      const categoryPromise = this.saveTaskCategory({
        project: this.selectedProject,
        category: this.task.material
      })

      /// --------------------------------------------------
      /// Tasks importation depending on the selected source
      /// --------------------------------------------------
      let sourcePromise = null

      // Amazon S3
      if (this.task.source === this.sources.amazon) {
        sourcePromise = this.importAmazonS3Tasks({
          project: this.selectedProject,
          bucket: this.bucket.name,
          files: this.task.sourceContent
        })

      }
      // Dropbox
      else if (this.task.source === this.sources.dropbox) {
        sourcePromise = this.importDropboxTasks({
          project: this.selectedProject,
          files: this.task.sourceContent
        })
      }
      // Flickr
      else if (this.task.source === this.sources.flickr) {
        sourcePromise = this.importFlickrTasks({
          project: this.selectedProject,
          albumId: this.task.sourceContent
        })
      }
      // Twitter
      else if (this.task.source === this.sources.twitter) {
        sourcePromise = this.importTwitterTasks({
          project: this.selectedProject,
          source: this.task.sourceContent.source,
          maxTweets: this.task.sourceContent.maxTweets
        })
      }

      /// --------------------------------------------------

      // test if all calls have been done correctly and redirects to the project detail page
      Promise.all([templatePromise,categoryPromise, sourcePromise]).then(results => {
        if (results.filter(el => el !== false).length === 3) {
          this.resetTaskBuilder()
          this.$router.push({ name: 'project', params: { id: this.selectedProject.id } })
        } else {
          this.showError({
            title: this.$t('error'),
            content: this.$t('summary-builder-promise-error')
          })
        }
      })

    }
  }
}
</script>

<style lang="scss" scoped>
  @import '../../../scss/variables.scss';

  .icon-secondary-big {
    color: $secondary;
    width: 3em;
    height: auto;
  }

  legend {
    padding-bottom: calc(0.375rem + 1px);
    margin-bottom: 0;
    font-size: inherit !important;
    line-height: 1.5;
  }

  .jumbotron {
    padding: 1rem !important;
  }

</style>
