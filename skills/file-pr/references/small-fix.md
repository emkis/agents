# Shape: small fix

Use for a small, self-contained fix, especially one that syncs behavior with something that already works correctly elsewhere. Keep it short: a sentence or two of context, no `Changes` or `Impact` headers — just enough to say what was broken and what it looks like now.

<example>
# Bypass Get started on demo accounts in Mobile app

Synchronizes Get Started's availability on the Mobile app with the Web app for demo accounts.

### Context

Demo accounts are accounts we create ourselves so customers can try out the product with most features already enabled, without going through onboarding or the usual checks. Since they are not real accounts, they can never complete onboarding themselves.

The Web app already accounts for this and shows Home directly for demo accounts, skipping Get Started. The Mobile app only bypassed onboarding and permission checks for Home, not for Get Started, so demo accounts ended up with both Home and Get Started as separate tabs.

### Demo
<table>
  <tr>
    <th>Before</th>
    <th>After</th>
  </tr>
  <tr>
    <td> ![ios-simulator-recording](video.mov){width=300} </td>
    <td> ![ios-simulator-recording](video.mov){width=300} </td>
  </tr>
</table>
</example>
