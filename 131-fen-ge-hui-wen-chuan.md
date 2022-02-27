```java
class Solution {
    List<List<String>> res = new ArrayList<>();
    public List<List<String>> partition(String s) {
       backtrack(s, 0, new ArrayList<String>(), res);
       return res;
    }
    public void backtrack(String s, int start, List<String> currList, List<List<String>> res){
        if(start >= s.length()){
            res.add(new ArrayList<String>(currList));
        }
        for(int end = start; end < s.length(); end++){
            if(isPalindrome(s, start, end)){
                currList.add(s.substring(start, end + 1));
                backtrack(s, end + 1, currList, res);
                currList.remove(currList.size() - 1);
            }
        }
    }
    public boolean isPalindrome(String s, int left, int right){
        while(left < right){
            if(s.charAt(left) != s.charAt(right)) return false;
            left++;
            right--;
        }
        return true;
    }
}

// class Solution {
//     public List<List<String>> partition(String s) {
//         List<List<String>> result = new ArrayList<List<String>>();
//         dfs(0, result, new ArrayList<String>(), s);
//         return result;
//     }

//     void dfs(int start, List<List<String>> result, List<String> currentList, String s) {
//         if (start >= s.length()) result.add(new ArrayList<String>(currentList));
//         for (int end = start; end < s.length(); end++) {
//             if (isPalindrome(s, start, end)) {
//                 // add current substring in the currentList
//                 currentList.add(s.substring(start, end + 1));
//                 dfs(end + 1, result, currentList, s);
//                 // backtrack and remove the current substring from currentList
//                 currentList.remove(currentList.size() - 1);
//             }
//         }
//     }

//     boolean isPalindrome(String s, int low, int high) {
//         while (low < high) {
//             if (s.charAt(low++) != s.charAt(high--)) return false;
//         }
//         return true;
//     }
// }
```

