<script>

  import { ApplicationShell } from "@typhonjs-fvtt/runtime/svelte/component/core";
  import { localize } from "@typhonjs-fvtt/runtime/svelte/helper";
	import { getContext } from "svelte";
  import { getSetting, setSetting } from "../../lib/lib.js";
  import CONSTANTS from "../../constants.js";
  import SettingsShim from "../settings/settings.js";
  import { gameSettings } from "../../settings.js";

  const { application } = getContext('external');

  export let elementRoot;

  let form;

  async function requestSubmit() {
    form.requestSubmit();
  }

  let restVariant = game.settings.get("dnd5e", "restVariant");
  let slowHealingEnabled = getSetting(CONSTANTS.SETTINGS.HP_MULTIPLIER) === CONSTANTS.FRACTIONS.NONE
		&& getSetting(CONSTANTS.SETTINGS.LONG_REST_ROLL_HIT_DICE);
  let bufferEnabled = getSetting(CONSTANTS.SETTINGS.PRE_REST_REGAIN_HIT_DICE);

  async function submitPrompt() {
    await game.settings.set("dnd5e", "restVariant", restVariant);
    await setSetting(CONSTANTS.SETTINGS.HP_MULTIPLIER, slowHealingEnabled ? CONSTANTS.FRACTIONS.NONE : CONSTANTS.FRACTIONS.FULL);
    await setSetting(CONSTANTS.SETTINGS.LONG_REST_ROLL_HIT_DICE, slowHealingEnabled);
    await setSetting(CONSTANTS.SETTINGS.PRE_REST_REGAIN_HIT_DICE, bufferEnabled);
    application.options.resolve();
    application.close();
  }

  async function openSettings(){
    new SettingsShim().render(true);
    application.close();
	}

</script>


<svelte:options accessors={true}/>

<ApplicationShell bind:elementRoot>

	<form autocomplete=off bind:this={form} class="dialog-content" on:submit|preventDefault={submitPrompt}>

		<div class="rest-recovery-flex-col">

			<h2>{localize("SETTINGS.5eRestN")}</h2>

			<select bind:value={restVariant}>
				<option value="normal">{localize("SETTINGS.5eRestPHB")}</option>
				<option value="gritty">{localize("SETTINGS.5eRestGritty")}</option>
				<option value="epic">{localize("SETTINGS.5eRestEpic")}</option>
			</select>

			<div>
				<div class="form-control">
					<input id="slow-natural-healing" type="checkbox" bind:checked={slowHealingEnabled}/>
					<label for="slow-natural-healing">
						{localize("REST-RECOVERY.Dialogs.QuickSetup.SlowNaturalHealingTitle")}
					</label>
				</div>

				<div class="small-text">{localize("REST-RECOVERY.Dialogs.QuickSetup.SlowNaturalHealingLabel")}</div>
			</div>

			<div>
				<div class="form-control">
					<input id="recover-before-starting-rest" type="checkbox" bind:checked={bufferEnabled}/>
					<label for="recover-before-starting-rest">
						{localize("REST-RECOVERY.Dialogs.QuickSetup.RecoverHitDiceTitle")}
					</label>
				</div>

				<div class="small-text">{localize("REST-RECOVERY.Dialogs.QuickSetup.RecoverHitDiceLabel")}</div>
			</div>

		</div>

		<footer class="flexrow">
			<button class="dialog-button" on:click={requestSubmit} type="button">
				<i class="fas fa-check"></i> {localize("Submit")}
			</button>
			<button class="dialog-button" on:click={openSettings} type="button">
				<i class="fas fa-cog"></i> {localize("REST-RECOVERY.Dialogs.QuickSetup.OpenSettings")}
			</button>
		</footer>

	</form>

</ApplicationShell>


<style lang="scss">


  .rest-recovery-flex-col {
    padding: 0.5rem;

    & > * {
      margin-bottom: 0.5rem;
    }
  }

  .form-control {
    display: flex;
    flex-direction: row;
    align-items: center;

    > input {
      margin-right: 0.5rem;
    }

    > label {
      font-size: 1rem;
    }
  }

  .small-text {
    font-size: 0.75rem;
    margin-top: 0.25rem;
    padding: 0 0.35rem;
  }

</style>
