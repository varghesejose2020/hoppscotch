<template>
  <AppSection id="script" :label="`${t('preRequest.script')}`">
    <div
      class="bg-primary border-dividerLight top-upperSecondaryStickyFold sticky z-10 flex items-center justify-between flex-1 pl-4 border-b"
    >
      <label class="text-secondaryLight font-semibold">
        {{ t("preRequest.javascript_code") }}
      </label>
      <div class="flex">
        <ButtonSecondary
          v-tippy="{ theme: 'tooltip' }"
          to="https://docs.hoppscotch.io/features/pre-request-script"
          blank
          :title="t('app.wiki')"
          svg="help-circle"
        />
        <ButtonSecondary
          v-tippy="{ theme: 'tooltip' }"
          :title="t('state.linewrap')"
          :class="{ '!text-accent': linewrapEnabled }"
          svg="corner-down-left"
          @click.native.prevent="linewrapEnabled = !linewrapEnabled"
        />
        <ButtonSecondary
          v-tippy="{ theme: 'tooltip' }"
          :title="t('action.clear')"
          svg="trash-2"
          @click.native="clearContent"
        />
      </div>
    </div>
    <div class="border-dividerLight flex border-b">
      <div class="border-dividerLight w-2/3 border-r">
        <div ref="preRrequestEditor"></div>
      </div>
      <div
        class="bg-primary top-upperTertiaryStickyFold min-w-46 max-w-1/3 z-9 sticky h-full p-4 overflow-auto"
      >
        <div class="text-secondaryLight pb-2">
          {{ t("helpers.pre_request_script") }}
        </div>
        <SmartAnchor
          :label="`${t('preRequest.learn')}`"
          to="https://docs.hoppscotch.io/features/pre-request-script"
          blank
        />
        <h4 class="text-secondaryLight pt-6 font-bold">
          {{ t("preRequest.snippets") }}
        </h4>
        <div class="flex flex-col pt-4">
          <TabSecondary
            v-for="(snippet, index) in snippets"
            :key="`snippet-${index}`"
            :label="snippet.name"
            active
            @click.native="useSnippet(snippet.script)"
          />
        </div>
      </div>
    </div>
  </AppSection>
</template>

<script setup lang="ts">
import { reactive, ref } from "@nuxtjs/composition-api"
import { usePreRequestScript } from "~/newstore/RESTSession"
import snippets from "~/helpers/preRequestScriptSnippets"
import { useCodemirror } from "~/helpers/editor/codemirror"
import linter from "~/helpers/editor/linting/preRequest"
import completer from "~/helpers/editor/completion/preRequest"
import { useI18n } from "~/helpers/utils/composables"

const t = useI18n()

const preRequestScript = usePreRequestScript()

const preRrequestEditor = ref<any | null>(null)
const linewrapEnabled = ref(true)

useCodemirror(
  preRrequestEditor,
  preRequestScript,
  reactive({
    extendedEditorConfig: {
      mode: "application/javascript",
      lineWrapping: linewrapEnabled,
      placeholder: `${t("preRequest.javascript_code")}`,
    },
    linter,
    completer,
  })
)

const useSnippet = (script: string) => {
  preRequestScript.value += script
}

const clearContent = () => {
  preRequestScript.value = ""
}
</script>
