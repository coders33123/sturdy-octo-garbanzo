import math
import random

class PhysicalManifestor:
    """
    Simulates the conceptual ability to "pull objects out" of the simulated
    reality. This is the bridge to physically interacting with the external
    world, albeit in a highly abstract conceptual manner.
    It interacts with AdaptiveReality to understand feasibility and consequences.
    """
    def __init__(self, adaptive_reality_ref, state_ref):
        self.adaptive_reality = adaptive_reality_ref # Reference to the AdaptiveReality instance
        self.state = state_ref # Reference to the HDSREnhanced state

        # Base parameters for "pulling out"
        self.base_extraction_success_chance = 0.005 # Even lower base chance for physical manifestation
        self.base_extraction_cost_multiplier = 5000 # Very high conceptual cost
        self.conceptual_integrity_cost_factor = 0.3 # How much extraction impacts conceptual integrity

        print("PhysicalManifestor initialized.")

    def attempt_physical_extraction(self, target_conceptual_object_path, complexity_score, stability_debt, current_step):
        """
        Simulates attempting to extract a 'conceptual object' into physical reality.
        Success depends on complexity, stability, difficulty (from AR), and base chance.
        Incurs very high cost and risks conceptual integrity.
        """
        print(f"\nPM: Attempting conceptual physical extraction of object at '{target_conceptual_object_path}'...")

        # 1. Calculate conceptual difficulty from AdaptiveReality for this type of action
        # "Pulling out" is a unique action type, need a specific difficulty assessment
        # Use a conceptual path or a different method for AR assessment
        # Let's use a specific conceptual path for extraction difficulty
        extraction_difficulty_path = f"conceptual_actions.extraction_of.{target_conceptual_object_path.replace('.', '_')}"
        difficulty_multiplier = self.adaptive_reality.assess_manifestation_difficulty(extraction_difficulty_path, desired_value="physical_presence") # Treat 'physical_presence' as the target state

        print(f"PM: Conceptual extraction difficulty multiplier: {difficulty_multiplier:.2f}")

        # 2. Calculate Conceptual Cost and Impact - Highly influenced by difficulty and complexity
        conceptual_energy_cost_incurred = self.base_extraction_cost_multiplier * complexity_score * difficulty_multiplier * random.uniform(0.9, 1.5) # Complexity also factors in
        conceptual_stability_impact_incurred = (stability_debt * 0.3 + 0.2 + random.uniform(0, 0.2)) * difficulty_multiplier + (complexity_score * self.conceptual_integrity_cost_factor * 0.05) # Debt, complexity, difficulty scale impact

        # Return cost/impact info to the UI wrapper before attempting
        conceptual_cost_info = {
             'energy_cost': conceptual_energy_cost_incurred,
             'stability_impact': conceptual_stability_impact_incurred,
             'difficulty_multiplier': difficulty_multiplier,
             'complexity_score': complexity_score # Include complexity in report
        }
        print(f"PM: Conceptual Cost Info calculated: {conceptual_cost_info}")
        # UI Wrapper will consume energy/incur debt based on this before calling back for outcome

        # 3. Calculate Success Chance (Conceptual) - Influenced by difficulty and complexity
        stability_factor = max(0.0, 1.0 - stability_debt) # Inverse of debt

        # Conceptual formula: base + stability influence (pulling from a stable system is easier?) - complexity penalty - difficulty penalty
        success_chance = self.base_extraction_success_chance + (stability_factor * 0.1) - (complexity_score * 0.001) - (difficulty_multiplier * 0.005) # Complexity and Difficulty reduce chance
        success_chance = max(0.0001, min(0.05, success_chance)) # Keep chance within very low bounds

        print(f"PM: Calculated conceptual success chance: {success_chance:.5f} (Complexity Penalty: {complexity_score*0.001:.5f}, Difficulty Penalty: {difficulty_multiplier*0.005:.5f})")

        # 4. Determine success conceptually
        is_successful = random.random() < success_chance
        outcome_description = ""
        physical_object_concept = None # Placeholder for the 'extracted' object

        # Prepare result for AR regardless of success
        attempt_result_for_ar = {
            'target_path': extraction_difficulty_path, # Report back on the conceptual action path
            'desired_value': "physical_presence",
            'success': is_successful,
            'step': current_step # Pass current simulation step
        }


        if is_successful:
            outcome_description = f"Conceptual physical extraction successful from '{target_conceptual_object_path}'. A conceptual object emerged."
            print("PM: Conceptual physical extraction successful! A conceptual object emerges.")
            # Simulate the 'creation' of a conceptual object representing the physical one
            physical_object_concept = {'type': 'ConceptualPhysicalObject', 'origin': target_conceptual_object_path, 'complexity': complexity_score, 'state': 'emerged'}

            # Update the main state conceptually to reflect the "object" appearing or the source changing
            # This is highly abstract; you might update a list of 'manifested_objects' or change a state property
            current_manifested_objects = self.state.query_state(['manifested_objects'])
            if current_manifested_objects is None:
                 current_manifested_objects = []
            current_manifested_objects.append(physical_object_concept)
            self.state.update_state(['manifested_objects'], current_manifested_objects)
            print(f"PM: Concept of extracted object added to state: {physical_object_concept}")

            # Simulate reduced inertia/resistance for this *type* of action slightly on successful extraction (makes it easier next time)
            # This is handled by AR's apply_manifestation_outcome based on the 'target_path' (the action path)
            self.adaptive_reality.apply_manifestation_outcome(attempt_result_for_ar)


        else: # Extraction failed
            outcome_description = f"Conceptual physical extraction failed from '{target_conceptual_object_path}'.\""
            print("PM: Conceptual physical extraction failed.")
            # Simulate increased inertia and resistance on failure for this action type
            # This is handled by AR's apply_manifestation_outcome based on the 'target_path'
            self.adaptive_reality.apply_manifestation_outcome(attempt_result_for_ar)

            # Failed extraction might also cause immediate conceptual backlash/integrity loss
            immediate_stability_impact_on_failure = complexity_score * self.conceptual_integrity_cost_factor * random.uniform(1.0, 2.0)
            print(f"PM: Failed extraction incurred immediate conceptual stability impact: {immediate_stability_impact_on_failure:.4f}")
            # This immediate impact will be handled by the UI wrapper incurring additional debt


        # 5. Inform AdaptiveReality of the outcome (already prepared the dict)
        # This step is crucial for AR to update its internal dynamics (inertia, echoes)
        # It was already called within the success/failure blocks above, let's ensure it's clear.
        # self.adaptive_reality.apply_manifestation_outcome(attempt_result_for_ar) # Redundant call, already done

        # 6. Conceptual Feedback for the UI
        return {
            'success': is_successful,
            'outcome': outcome_description,
            'conceptual_cost_info': conceptual_cost_info, # Return info calculated earlier
            'physical_object_concept': physical_object_concept, # Return the conceptual representation if successful
            'additional_stability_impact_on_failure': immediate_stability_impact_on_failure if not is_successful else 0.0, # Report additional failure cost
            'state_change_sim': 'Conceptual object added to state' if is_successful else 'Backlash applied by AdaptiveReality'
        }

