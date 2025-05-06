from calculations import (
    calculate_target_mean_strength, get_max_w_c_ratio, get_w_c_ratio_from_chart,
    get_water_content, adjust_water_content_for_slump, adjust_water_content_for_admixture,
    calculate_cement_content, adjust_aggregate_proportions, plot_w_c_ratio_chart
)
from data_storage import f_ck_values

def get_user_inputs():
    grade = input("Enter the grade of concrete (e.g., 'M20'): ")
    concrete_type = input("Enter the type of concrete (PCC for Plain, RCC for Reinforced): ")
    exposure_condition = input("Enter the exposure condition (e.g., 'Mild', 'Moderate', etc.): ")
    cement_type = input("Enter the type of cement (e.g., 'OPC 33', 'OPC 43', 'OPC 53', 'PPC', 'PSC'): ")
    coarse_aggregate_size = int(input("Enter the size of the coarse aggregate (10, 20, 30 mm): "))
    target_slump = int(input("Enter the target slump value (in mm): "))
    admixture_used = input("Is an admixture used? (yes/no): ").strip().lower() == 'yes'
    fine_aggregate_zone = input("Enter the zone of the fine aggregate (Zone I, Zone II, Zone III, Zone IV): ")
    pump_required = input("Is concrete pumping required? (yes/no): ").strip().lower() == 'yes'
    return grade, concrete_type, exposure_condition, cement_type, coarse_aggregate_size, target_slump, admixture_used, fine_aggregate_zone, pump_required

def main():
    grade, concrete_type, exposure_condition, cement_type, coarse_aggregate_size, target_slump, admixture_used, fine_aggregate_zone, pump_required = get_user_inputs()

    f_ck = f_ck_values.get(grade, 0)

    if f_ck == 0:
        print(f"Invalid grade of concrete: {grade}")
        return

    target_mean_strength = calculate_target_mean_strength(f_ck, grade)
    print(f"The target mean strength (f'ck) for grade {grade} is: {target_mean_strength:.2f} N/mm^2")

    max_w_c_ratio = get_max_w_c_ratio(exposure_condition, concrete_type)
    chart_w_c_ratio = get_w_c_ratio_from_chart(target_mean_strength, cement_type)

    if max_w_c_ratio == "Unknown exposure condition" or chart_w_c_ratio == "Compressive strength out of range" or max_w_c_ratio == "Unknown concrete type" or chart_w_c_ratio == "Unknown cement type":
        print("Invalid input provided.")
        return

    final_w_c_ratio = min(max_w_c_ratio, chart_w_c_ratio)
    print(f"The maximum water-cement ratio for {exposure_condition} exposure condition and {concrete_type} is: {max_w_c_ratio}")
    print(f"The water-cement ratio from the chart for {target_mean_strength} N/mm^2 and {cement_type} is: {chart_w_c_ratio}")
    print(f"The final water-cement ratio to be used is: {final_w_c_ratio}")

    try:
        initial_water_content = get_water_content(coarse_aggregate_size)
    except ValueError as e:
        print(e)
        return

    adjusted_water_content = adjust_water_content_for_slump(initial_water_content, target_slump)
    final_water_content = adjust_water_content_for_admixture(adjusted_water_content, admixture_used)
    print(f"The final water content to be used is: {final_water_content:.2f} kg/m³")

    cement_content, min_cement_content, max_cement_content = calculate_cement_content(final_water_content, final_w_c_ratio, exposure_condition, concrete_type)
    print(f"The calculated cement content is: {cement_content:.2f} kg/m³")
    print(f"Minimum cement content for {exposure_condition} exposure condition and {concrete_type} is: {min_cement_content} kg/m³")
    print(f"Maximum cement content for {exposure_condition} exposure condition and {concrete_type} is: {max_cement_content} kg/m³")

    coarse_aggregate_ratio = adjust_aggregate_proportions(coarse_aggregate_size, fine_aggregate_zone, final_w_c_ratio, pump_required)
    fine_aggregate_ratio = 1 - coarse_aggregate_ratio
    print(f"The adjusted coarse aggregate ratio is: {coarse_aggregate_ratio:.3f}")
    print(f"The adjusted fine aggregate ratio is: {fine_aggregate_ratio:.3f}")

    plot_w_c_ratio_chart(cement_type)

if __name__ == "__main__":
    main()
