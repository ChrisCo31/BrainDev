---
title: "Reflection Pattern: When Agents think twice"
source: "https://theneuralmaze.substack.com/p/reflection-pattern-agents-that-think"
author:
  - "[[Miguel Otero Pedrido]]"
published: 2024-12-04
created: 2026-07-01
description: "Agentic Patterns From Scratch: Lesson 1"
tags:
  - "clippings"
---
Having explored **what an Agent is** - [check Lesson 0 of the series!](https://theneuralmaze.substack.com/p/what-is-an-agent) - it’s now time to get started with the **Agentic Patterns**, as introduced by **Andrew Ng** in his [DeepLearning.ai series](https://www.deeplearning.ai/the-batch/how-agents-can-improve-llm-performance/?ref=dl-staging-website.ghost.io).

We won’t just stop at understanding these patterns - we’ll implement each one from scratch, using pure Python and [Groq](https://console.groq.com/docs/models) LLMs.

**But … wait! Why not use frameworks like LlamaIndex or CrewAI? 🤔**

Well, I thought that, in order to truly understand what was going on under the hood, it made sense to get rid of all the frameworks and **implement everything ourselves.**

We’ll start with the first pattern; the **Reflection Pattern**, where the LLM evaluates its own outputs to suggest modifications, improvements and refinements to enhance the final result.

**Let’s begin! 🧑💻**

> You can find the code for the whole series on [GitHub](https://github.com/neural-maze/agentic_patterns)!

![](https://substackcdn.com/image/fetch/$s_!Rd6P!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fa610f8fb-72a0-442e-a801-affbe592e0b3_1080x1080.png)

---

## Reflection Pattern 101

Although the **Reflection Pattern** is **the simplest** of all the patterns, it provides surprising **performance gains** for the LLM response.

As mentioned, this pattern allows the Agent to reflect on its generated output, providing feedback to progressively improve the final result.

**Isn’t that great? 😍**

What if I told you that this **reflection mechanism is as simple as a loop**? Just like the one you see below?

![](https://substackcdn.com/image/fetch/$s_!_7kS!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fc510fecf-7b74-4382-bbdc-f9d7b7c339f2_1080x1080.png)

The **reflection loop** can be divided into the following **four steps**:

🔹 The LLM generates an output (let's call it the 𝐠𝐞𝐧𝐞𝐫𝐚𝐭𝐢𝐨𝐧 𝐩𝐫𝐨𝐜𝐞𝐬𝐬)

🔹 The LLM corrects the ouptut generated in the previous step (let's call it the 𝐫𝐞𝐟𝐥𝐞𝐜𝐭𝐢𝐨𝐧 𝐩𝐫𝐨𝐜𝐞𝐬𝐬)

🔹 The LLM takes the corrections and uses them to modify the original output accordingly.

🔹 A new iteration starts.

---

**Now the question is: when should we stop this loop? Or does it run forever? 😅**

Well, generally we can stablish **two stopping criteria** for the iteration:

🔸 Loop for a 𝐟𝐢𝐱𝐞𝐝 𝐧𝐮𝐦𝐛𝐞𝐫 𝐨𝐟 𝐢𝐭𝐞𝐫𝐚𝐭𝐢𝐨𝐧𝐬  
  
🔸 Loop until the 𝐋𝐋𝐌 𝐠𝐞𝐧𝐞𝐫𝐚𝐭𝐞𝐬 𝐚 𝐬𝐭𝐨𝐩 𝐬𝐞𝐪𝐮𝐞𝐧𝐜𝐞, indicating the result is satisfactory. For example, **"OK"**, **"Correct"**, etc.

---

## Reflection Loop From Scratch

Now that we understand the theory, it’s time to jump straight into the code. Let’s begin with the generation step.

### 💠 Generation Step

The first thing we need to consider for this implementation is:

> What do we want to generate? A poem? An essay? Python code?

For this example, I’ve decided to test the Python coding skills of **Llama3 70B** - the Groq hosted version of this model. In particular, we are going to ask our LLM to code a famous sorting algorithm: **Merge Sort.**

So, first of all, let’s create the Groq client and import all the relevant libraries.

![](https://substackcdn.com/image/fetch/$s_!BGud!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fb5c39df8-2a1d-4c57-a86d-5c72ab0dd6e2_3544x1368.png)

We’ll start the **"generation"** chat history with the system prompt - as is tyically done. In this case, let’s make the LLM act like a Python programmer eager to receive feedback / critique from the user.

Let’s also add a new message to the chat history asking about the implementation of the Merge Sort agorithm. Finally, just call the completions endpoint and generate the first version of the Merge Sort code.

![](https://substackcdn.com/image/fetch/$s_!HB6X!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Ff83fdc47-522f-432f-bc2a-d31b797048fc_3932x2752.png)

If you run this piece of code, you should get something like this:

```markup
def merge_sort(arr):
    """
    Sorts an array using the Merge Sort algorithm.

    Time complexity: O(n log n)
    Space complexity: O(n)

    :param arr: The array to be sorted
    :return: The sorted array
    """
    if len(arr) <= 1:
        return arr

    mid = len(arr) // 2
    left = arr[:mid]
    right = arr[mid:]

    left = merge_sort(left)
    right = merge_sort(right)

    return merge(left, right)

def merge(left, right):
    """
    Merges two sorted arrays into a single sorted array.

    :param left: The first sorted array
    :param right: The second sorted array
    :return: The merged sorted array
    """
    result = []
    i, j = 0, 0

    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1

    result.extend(left[i:])
    result.extend(right[j:])

    return result

# Example usage:
arr = [5, 2, 8, 3, 1, 6, 4]
arr = merge_sort(arr)
print(arr)  # [1, 2, 3, 4, 5, 6, 8]
```

This is just the first version of the Merge Sort algorithm implementation. It’s time to reflect!

### 💠 Reflection Step

Let's allow the LLM to reflect on its outputs by defining another system prompt. This prompt will tell the LLM to act as **Andrej Karpathy**, computer scientist and Deep Learning wizard.

> To be honest, I don't think having the LLM act like Andrej Karpathy will significantly influence the LLM outputs, but it sure was fun! 😛

![Alt text](https://substackcdn.com/image/fetch/$s_!mpdH!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Ffbddf573-dded-4704-b372-c4ed84343d38_1024x1024.png)

As we did before, let’s create a chat history, but this time, for the **reflection phase**. The user message, in this case, will be the code generated in the previous step - the output from the generation phase.

We simply add the `mergesort_code` variable to the `reflection_chat_history` and call the completions endpoint again.

![](https://substackcdn.com/image/fetch/$s_!O0-V!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F93cc6c05-6494-44dd-bc54-f92d9b7b1c51_3932x2208.png)

After running this code, you’ll see Karpathy’s amazing suggestions. Here’s what I got in my execution (you might receive different suggestions):

```markup
Excellent implementation!

Here are some minor suggestions for improvement and critique:

Consistent whitespace: In the merge_sort function, you have an inconsistent number of spaces between the if statement and the mid assignment. Python's PEP 8 recommends using 4 spaces for indentation. Also, there's an extra space between the return statement and the merge function call. Remove the extra space for consistency.

Type hints: You've provided excellent docstrings, but adding type hints for the function parameters and return types can make the code more readable and self-documenting. For example, def merge_sort(arr: list[int]) -> list[int]:. This is especially useful for other developers who might not read the docstrings.

Variable naming: The variable names left and right are clear, but you could consider renaming them to left_half and right_half to emphasize that they're halves of the original array. Similarly, i and j could be renamed to left_index and right_index to clearly indicate their purpose.

Merge function: The merge function is well-written, but you could consider adding a brief comment to explain the logic. For example, # Merge smaller elements first, then append the remaining elements from each half.

Example usage: The example usage is great, but you might want to consider adding a brief comment to indicate that the example array is unsorted and will be sorted using the merge_sort function.

Edge cases: Your implementation handles the edge case where the input array is empty or has only one element correctly. However, you might want to consider adding a brief comment or docstring to explicitly state that the function handles these edge cases.
```

Finally, we just need to add this **critique** to the `generation_chat_history`

![](https://substackcdn.com/image/fetch/$s_!7YhL!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F1b56679a-1d36-4815-b92a-d28d103ae0ca_1604x1068.png)

Now, the generation block will start again, incorporating Karpathy’s feedback. The updated result will be passed to Karpathy once more, who will provide additional feedback, and so on. The reflection loop will continue iterating, over and over again, either for a set number of steps or until a stop sequence is encountered.

But that’s basically it! Easy, right? Of course, this isn’t a “robust” or “production-ready” implementation. Don’t worry though, I’ve got you covered 😏

In the [repo attached to this series](https://github.com/neural-maze/agentic_patterns/blob/main/src/agentic_patterns/reflection_pattern/reflection_agent.py), you’ll find the implementation of a **Reflection Agent**, which is essentially an abstraction that encapsulates the reflection loop, allowing you to interact with it in a simple and clean way.

**Let’s see it in action in the next section!** 💪

---

## The Reflection Agent

The first thing you’ll need to do is install the **agentic-patterns** library, that contains the **Reflection Agent** implementation.

```markup
pip install -U agentic-patterns
```

After you have the library installed and all the env variables set - more on this in the repo - it’s time to try out the **Reflection Agent!**

![](https://substackcdn.com/image/fetch/$s_!Odds!,w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F38e67c46-6a3e-4da1-8d79-c16f99c4cd15_4188x2272.png)

And here is my `final_response:`

```markup
class MergeSort:
    def __init__(self, arr: list):
        """
        Initializes the MergeSort class with an input list of elements.

        Args:
        arr (list): Input list of elements to be sorted.

        Raises:
        ValueError: If the input is not a list.
        """
        if not isinstance(arr, list):
            raise ValueError("Input must be a list")
        self.arr = arr

    def merge_sort(self, arr: list = None) -> list:
        """
        Sorts an input list of elements using the Merge Sort algorithm.

        This implementation uses a divide-and-conquer approach, recursively splitting
        the input list into two halves until the base case is reached (i.e., when the
        length of the list is one or zero). The sorted halves are then merged using the
        \`_merge\` function.

        Time Complexity:
            O(n log n)
        Space Complexity:
            O(n)
        """
        if arr is None:
            arr = self.arr

        # Check if the input is a list.
        if not isinstance(arr, list):
            raise ValueError("Input must be a list")

        # Base case: If the input list is empty or has one or zero elements, it is already sorted.
        if len(arr) <= 1:
            return arr

        mid = len(arr) // 2
        left_half = arr[:mid]
        right_half = arr[mid:]

        # Recursively sort each half.
        left = self.merge_sort(left_half)
        right = self.merge_sort(right_half)

        # Merge the two sorted halves.
        return self._merge(left, right)

    def _merge(self, left: list, right: list) -> list:
        """
        Merges two sorted lists into a single sorted list.

        Args:
        left (list): First sorted list.
        right (list): Second sorted list.

        Returns:
        list: Merged sorted list.
        """
        merged = []
        left_index = 0
        right_index = 0

        while left_index < len(left) and right_index < len(right):
            if left[left_index] <= right[right_index]:
                merged.append(left[left_index])
                left_index += 1
            else:
                merged.append(right[right_index])
                right_index += 1

        # Append any remaining elements.
        merged.extend(left[left_index:])
        merged.extend(right[right_index:])

        return merged

# Example usage
if __name__ == "__main__":
    merge_sort_instance = MergeSort([64, 34, 25, 12, 22, 11, 90])
    sorted_arr = merge_sort_instance.merge_sort()
    print("Sorted array:", sorted_arr)

# Testing
import unittest

class TestMergeSort(unittest.TestCase):
    def test_sorted_array(self):
        arr = [64, 34, 25, 12, 22, 11, 90]
        merge_sort_instance = MergeSort(arr)
        sorted_arr = merge_sort_instance.merge_sort()
        self.assertEqual(sorted_arr, [11, 12, 22, 25, 34, 64, 90])

    def test_empty_list(self):
        arr = []
        merge_sort_instance = MergeSort(arr)
        sorted_arr = merge_sort_instance.merge_sort()
        self.assertEqual(sorted_arr, [])

    def test_single_element(self):
        arr = [5]
        merge_sort_instance = MergeSort(arr)
        sorted_arr = merge_sort_instance.merge_sort()
        self.assertEqual(sorted_arr, [5])

    def test_duplicate_elements(self):
        arr = [2, 4, 2, 1, 4]
        merge_sort_instance = MergeSort(arr)
        sorted_arr = merge_sort_instance.merge_sort()
        self.assertEqual(sorted_arr, [1, 2, 2, 4, 4])

    def test_negative_numbers(self):
        arr = [-3, 1, -2, 5, 0]
        merge_sort_instance = MergeSort(arr)
        sorted_arr = merge_sort_instance.merge_sort()
        self.assertEqual(sorted_arr, [-3, -2, 0, 1, 5])

if __name__ == "__main__":
    unittest.main()
```

Phew!! That’s a huge difference! Classes, unit tests … a **clear improvement** from the first version of the code, all thanks to the reflection loop. As I told you at the beginning of this post, despite its simplicity, **the Reflection Pattern works remarkably well!** 😯

**If you prefer video lectures, I also have a YouTube video covering the Reflection Pattern! 👇**

![](https://www.youtube.com/watch?v=0sAVI8bQdRc)

---

That’s all for today! Next week, we’ll talk about **Tools**, the **secret ingredient** of any agentic system. But until then …

**Happy coding! 👋**

Miguel