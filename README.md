# ai201-project3-takemeter

# confusion_matrix
cm = confusion_matrix(ft_true_ids, ft_pred_ids)
disp = ConfusionMatrixDisplay(confusion_matrix=cm, display_labels=label_names)
fig, ax = plt.subplots(figsize=(7, 5))
disp.plot(ax=ax, cmap="Blues", colorbar=False)
ax.set_title("Fine-Tuned Model — Confusion Matrix (Test Set)")
plt.tight_layout()
plt.savefig("confusion_matrix.png", dpi=150)
plt.show()
print("✅ Saved: confusion_matrix.png  →  commit this to your repo and include in README")



wrong_idx = np.where(ft_pred_ids != ft_true_ids)[0] --> it does not know where to map the results
      5 print(f"Wrong predictions: {len(wrong_idx)} / {len(ft_true_ids)}\n")--> there is no defined length
      6 

NameError: name 'np' is not defined


# AI Usage
I asked AI to to generate 5–10 posts that sit at the boundary between two labels and identify patterns.
