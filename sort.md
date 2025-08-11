```
class Solution {
    public int[] sortArray(int[] nums) {
        if(nums == null || nums.length < 2) {
            return nums;
        }
        int n = nums.length;
        int[] tmpNums = new int[n];
        mergeSort(nums, tmpNums, 0, n-1);
        return nums;
    }

    private void mergeSort(int[] nums, int[] tmpNums, int left, int right){
        if (left < right){
            int mid = left + (right - left) / 2;
            mergeSort(nums, tmpNums, left, mid);
            mergeSort(nums, tmpNums, mid + 1, right);
            merge(nums, tmpNums, left, mid, right);
        }
    }

    private void merge(int[] nums, int[] tmpNums, int left, int mid, int right){
        for (int i = left; i <= right; i++) {
            tmpNums[i] = nums[i];
        }
        int i = left, j = mid + 1, k = left;
        while (i <= mid && j <= right) {
            if (tmpNums[i] <= tmpNums[j]){
                nums[k++] = tmpNums[i++];
            }else {
                nums[k++] = tmpNums[j++];
            }
        }

        while (i <= mid){
            nums[k++] = tmpNums[i++];
        }
        while (j <= right) {
            nums[k++] = tmpNums[j++];
        }
    }


    private void quickSort(int[] nums, int start, int end){
        if (start <= end){
            int pivotIndex = partition(nums, start, end);
            quickSort(nums, start, pivotIndex - 1);
            quickSort(nums, pivotIndex + 1, end);
        }
    }

    private int partition(int[] nums, int low, int high){
        int pivot = nums[low + (high - low) / 2];
        swap(nums, low, low + (high - low) / 2);
        int left = low+1, right = high;
        while (left <= right){
            while (left <= right && nums[right] >= pivot){
                right --;
            }
            while (left <= right && nums[left] < pivot){
                left ++;
            }

            if (left <= right){
                swap(nums, left, right);
                left ++;
                right --;
            }
        }
        nums[low] = nums[right];
        nums[right] = pivot;
        return right;
    }

    private void swap(int[] nums, int i, int j){
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```