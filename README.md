# assesment-of-32-bit-floating-point-format
def decimal_to_ieee754(decimal_value):
    """Converts a decimal value to its IEEE 754 32-bit floating-point representation.

    Args:
        decimal_value: The decimal number to convert.

    Returns:
        A string representing the 32-bit IEEE 754 representation (e.g., "0 10000000 00000000000000000000000").
        Returns an error message if the input is not a valid number or if it's NaN or infinity.
    """
    try:
        decimal_value = float(decimal_value)
    except ValueError:
        return "Invalid input: Not a valid number."

    if decimal_value == 0:
        return "0 00000000 00000000000000000000000"  # Special case for zero

    if decimal_value != decimal_value:  # Check for NaN
        return "NaN"

    if decimal_value == float('inf'):
        return "0 11111111 00000000000000000000000"  # Positive Infinity
    if decimal_value == float('-inf'):
        return "1 11111111 00000000000000000000000"  # Negative Infinity



    sign = 1 if decimal_value < 0 else 0
    decimal_value = abs(decimal_value)

    integer_part, fractional_part = str(decimal_value).split(".")  # handles integers too

    integer_binary = bin(int(integer_part))[2:]
    fractional_binary = ""

    fractional_part = float("0." + fractional_part)

    while fractional_part > 0 and len(fractional_binary) < 23:
        fractional_part *= 2
        if fractional_part >= 1:
            fractional_binary += "1"
            fractional_part -= 1
        else:
            fractional_binary += "0"

    if integer_binary:  # Normalized case
        exponent = len(integer_binary) - 1
        mantissa = integer_binary[1:] + fractional_binary
        mantissa = mantissa[:23]  # Truncate or pad with zeros

    else:  # Denormalized case
        first_one = fractional_binary.find('1')
        if first_one == -1:  # Handle 0.0 (already covered above but added here for completeness)
            return "0 00000000 00000000000000000000000"
        exponent = -(first_one + 1)
        mantissa = fractional_binary[first_one + 1:]
        mantissa = mantissa[:23]  # Truncate or pad with zeros



    biased_exponent = exponent + 127
    exponent_binary = bin(biased_exponent)[2:].zfill(8)


    return f"{sign} {exponent_binary} {mantissa.ljust(23, '0')}"



# Example usage:
while True:
    decimal_input = input("Enter a decimal number (or 'exit' to quit): ")
    if decimal_input.lower() == 'exit':
        break
    ieee754_representation = decimal_to_ieee754(decimal_input)
    print(ieee754_representation)

# print(decimal_to_ieee754(0.0)) # 0 00000000 00000000000000000000000 (zero)
