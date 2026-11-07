library(readxl)
bp1 <- read_xlsx(file.choose())

bp1$Outcome <- factor(bp1$Outcome, levels = c(0, 1),
                      labels = c("No diabetes", "Diabetes"))
cols <- c("Glucose", "BloodPressure", "SkinThickness", "Insulin", "BMI")
bp1[cols] <- lapply(bp1[cols], function(x) replace(x, x == 0, NA))
hist(bp1$Insulin, breaks = 30, main = "Insulin distribution", xlab = "Insulin")
# correlation heatmap
cor_mat <- cor(bp1[, -9], use = "complete.obs")
round(cor_mat, 2)
heatmap(cor_mat, symm = TRUE, margins = c(10, 10))

# glucose density by outcome
install.packages("ggplot2")   # once
library(ggplot2)
ggplot(bp1, aes(x = Glucose, fill = Outcome)) +
  geom_density(alpha = 0.5) +
  labs(title = "Glucose distribution by outcome")
summary(bp1)
t.test(Glucose ~ Outcome, data = bp1)
t.test(BMI ~ Outcome, data = bp1)
wilcox.test(Insulin ~ Outcome, data = bp1)
model <- glm(Outcome ~ Glucose + BMI + Age, data = bp1, family = binomial)
summary(model)
round(exp(coef(model)), 2)    # odds ratios 
